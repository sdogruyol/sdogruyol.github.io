---
title: "Why I Built iyi: 10 Years of Crystal, and the One Rule I Couldn't Add"
subtitle: "A language that keeps Crystal's syntax and drops the rule that makes it slow."
date: 2026-11-03 09:00:00 +0300
categories: programming languages crystal iyi
description: "iyi is a compiled language that keeps Crystal's syntax and compiles modules against declarations instead of bodies. Here is why I built it, what measurement showed, and what it cost."
---

I have been writing Crystal for over ten years. I built Kemal in it, I maintain libraries in it, and I still think it is one of the nicest languages to read and write. So the sentence I am about to write is not the beginning of a complaint about Crystal. It is the beginning of an explanation.

For ten years, my edit loop has been this: change one line, wait, change another line, wait. On a small project the wait is a second. On a project with a few dozen files it is two or three. You stop noticing it after a while, the way you stop noticing a chair that is slightly too low. Then one day you are working on something larger, you change one number inside one function, and you sit there watching a progress indicator that has nothing to tell you. I started timing it.

One line changed in a project of 30 modules and 7,207 lines. Rebuild with Crystal: **1.17 seconds**. The same edit, with the same compiler binary, under a different set of rules: **0.13 seconds**.

That 9x is what this post is about. Not the number itself, but where it comes from, why it cannot be patched into Crystal, and what I had to give up to get it.

## Measuring instead of guessing

Before I wrote a line of a new compiler, I spent months putting timers inside Crystal's. This was not a small detour. It was the whole point. Almost every language project starts with an intuition about what is slow, and most of those intuitions are wrong in a specific way: they point at the compiler when the problem is the language.

The first thing I measured was an incremental build, one comment changed, every object file reused. The numbers looked like this:

| Phase | Time (s) | Share |
|---|---|---|
| Semantic analysis, instantiation cascade | 10.28 | 35% |
| LLVM IR generation | 8.91 | 30% |
| Semantic analysis, top level | 3.99 | 14% |
| Semantic analysis, main | 3.84 | 13% |
| Object code (parallel, cached) | 1.27 | 4% |
| Linking | 0.80 | 3% |

Twenty-seven seconds of wall time, and 93% of it in semantic analysis and IR generation, all of it on one core. On a machine with 32 cores. That asymmetry alone tells you that the work is not parallelizable, and work that is not parallelizable is usually work that depends on everything else.

So I measured the dependency directly. I added 200 typed functions to a program, then 1000. Here is what happened:

| User code | Semantic (top level) | Semantic (main) | IR generation |
|---|---|---|---|
| 0 functions (a single `puts`) | 0.263 | 0.046 | 0.202 |
| 200 typed functions | 0.278 | 0.116 | 0.115 |
| 1000 typed functions | 0.268 | 0.125 | 0.132 |

Adding a thousand typed functions costs **+0.08 seconds**. Essentially free. And that single row explains the whole problem: about 95% of the non-LLVM work in any normal Crystal program is a fixed tax. It is the standard library being re-analysed from source every single time, because nothing in the language lets the compiler trust what it read last build.

I then went after the instantiation cascade, which was 35% of the incremental build on its own. The hypothesis was that one pathological initializer was responsible. It was not. One initializer accounted for 99.7% of the phase, so I converted it into a lazy getter, and the cost simply moved to the next initializer. Phase time went from 10.28 to 10.22 seconds. Whichever substantial initializer runs first absorbs the cost of instantiating the program's method graph. That work is real. It is not a bug you can find and fix.

The last measurement was the one that changed the design. I instrumented the compiler to record every method instantiation twice: once by exact type, and once by what Go calls a GC shape, which is the memory layout the code actually depends on. The difference between the two counts is work a dictionary-passing implementation would never do.

| Workload | Instantiations | Shape-collapsed | Eliminated |
|---|---|---|---|
| Crystal compiler self-build | 64,222 | 34,282 | 46.6% |
| 60 model types over Array/Hash/Set | 14,905 | 7,792 | 47.7% |
| Bare `puts "hello"` (prelude only) | 4,383 | 3,895 | 11.1% |

Two very different workloads agreeing at roughly 47%. `Crystal::TypeNode#accept` goes from 442 instantiations to 1. `String#==` goes from 414 to 11. Many receiver types, all pointers, one body.

(A detail worth mentioning because it nearly cost me the result: my first census run said 26.9%, and the generics-heavy workload came out at an implausible 2.6%. I had bucketed instantiations by owner and name, so `Array(Model1)#push` and `Array(Model2)#push` landed in different buckets. The measurement had partitioned the data by exact type and hidden the effect. Corrected to global sets, the real figures are the ones above.)

## The rule I could not add

By this point I knew what was expensive. The obvious next question was whether it could be fixed inside Crystal. The answer is no, and the reason is one feature.

A Crystal class is open until the last line of the last file in the program. Any file can reopen `String`. That is not a wart, it is a feature people use, and it is the reason Crystal has the standard library it has. It is also the reason no build may trust anything it read last time: a `String` method you did not write can appear in a file you never opened, so the compiler has to re-read everything on every build.

I checked how deep this goes. Of 484 types in Crystal's standard library, **77 are reopened across module boundaries**. `String` alone is reopened by `json`, `yaml`, `uri`, `log` and `big`. There is no version of "just cache the analysis" that survives this. The analysis is not cacheable because its inputs are not bounded.

The same argument applies, one layer down, to global type inference and whole-program monomorphisation. `macro_run`, which compiles and runs a program in the middle of a build, was measured at **21% of a cold build** from a single call site. Every one of these is a design decision with real benefits. Every one of them makes separate compilation impossible.

So the honest conclusion was: you cannot make Crystal build like Go by patching its compiler. You have to change the rule about what a module is allowed to know. And once you change that rule, you are not modifying a language anymore. You are designing one.

## What survives, and what does not

Here is the thing that surprised me. Almost nothing that makes Crystal pleasant to write is expensive.

Blocks, union types, nil-safety, everything-is-an-expression, local inference, `end`-style syntax, postfix modifiers, named and default arguments, operator overloading, fibers and channels. All free. None of them require the compiler to know about the rest of the program. Crystal's reputation for expressiveness rests almost entirely on features that cost nothing to compile.

What costs is invisible to the person writing code. Open classes. Global inference. Whole-program monomorphisation. Unrestricted macros. `require` as textual inclusion.

So iyi keeps the first list and replaces the second with five rules borrowed from Go, each one load-bearing:

1. **A module is the unit of compilation.** `import` forms a DAG, and compiling a module reads only its dependencies' export metadata. Each module produces an `.iyimod` file, keyed by content hash. Modules compile in parallel, which is also the first time your other cores get used.
2. **Signatures required on exports, optional everywhere else.** `pub def` needs full parameter and return types. Everything else infers, because the module is the recompilation unit.
3. **Traits with coherence, replacing open classes.** An `impl Trait for Type` must live in the module that defines the trait or the type. A type's method set is knowable without reading the whole program.
4. **Dictionaries by default, monomorphisation on request.** Generic calls crossing a module boundary pass a dictionary keyed on GC shape. Inside a module, monomorphise. `@[Monomorphize]` forces specialisation across a boundary where you have measured that it matters.
5. **Macros scoped to derives.** A macro sees the declaration it is attached to and its own module. Expansion caches per module. Global reflection and mid-build program execution are gone.

## The acceptance test

Design documents are cheap. The test that matters is whether the hardest thing in an ecosystem can be written under the new rules.

In Crystal, that thing is Kemal's router. It generates its entire HTTP verb surface with a macro loop, it defines methods at the top level so that `require "kemal"` injects a DSL into your program, and it leans on open classes and runtime dispatch throughout. If iyi could not express it, the design was wrong and I wanted to know that in month three, not year two.

I ported `router.cr` and `dsl.cr` by hand. 337 lines and 218 lines of Crystal became 180 code lines against Kemal's 173, a 4% increase, with the structure unchanged. `record` ported as-is. The macro loops ported as-is. Blocks, `yield`, `case`/`in`, all as-is.

Three things came out of doing it for real.

**The constraint improved the code.** Kemal's route blocks may return anything. The return type is `_`, and the result is coerced at runtime, on every request, forever. In iyi the handler's return type is a type parameter bounded by a trait, so the check happens at compile time and a user can make their own type returnable by implementing it. The rule forced a better design.

**The macro loop was fine.** This was the biggest worry going in. Kemal generates its verb surface with `{% raw %}{% for method in HTTP_METHODS %}{% endraw %}` over a module-local constant, emitting declarations into its own module. Legal under derive-scoped macros. Nothing to cache-bust.

**The design needed one more feature.** Kemal defines `get`, `post`, `before_all` and `use` at the top level, which is exactly the same problem as an open class, and it is Kemal's entire identity. Making people write `kemal.get "/"` would be safe and would kill the framework. The fix was to move the decision to the consumer:

```crystal
module app

import kemal/dsl

get "/users/:id" do |env|
  User.find(env.params.url["id"]).to_json
end
```

I did not get that from thinking. I got it from porting.

## What is built now

The current release is 0.20.0. Three months ago this was a specification. The standard library speaks TLS, HTTP, and WebSocket in iyi, from the RFCs, with no OpenSSL or libcurl underneath. The garbage collector is iyi's as well. A hello world is 36 KB and starts in 1.6 milliseconds; the same program with Crystal's library is 1,553 KB and 3.2 ms. `iyi lsp` and `iyi mcp` are shells around the same front end a build uses, so an editor or an agent sees what the compiler sees.

There is an ecosystem starting at [packs.iyi.dev](https://packs.iyi.dev): the web framework I ported, database drivers, a TOML parser and a Slim-inspired templating engine from people I have never met. One of them built that registry for a language that was weeks old. That last one did more for my confidence than any benchmark.

## What it costs

A post like this is easy to write dishonestly, so here is the other column.

A full build from scratch is slower than Go: 0.24 seconds for 6,912 unseen lines against `go build`'s 0.09. iyi wins the edit loop, which is the loop you spend your day in, and loses the cold build. That is a real trade, not a rounding error.

Dictionary passing has a runtime cost. I measured it directly, and it is not one number. A dictionary call costs roughly 3 to 4 cycles, so against a vectorisable numeric loop the penalty is 17.5x, against a loop LLVM cannot vectorise anyway it is 1.21x, and against code doing real work per element it is free. The default is sound but the cliff is real, which is why `@[Monomorphize]` exists and why the compiler should eventually warn when a hot generic crosses a boundary.

Losing open classes is a genuine loss, and while traits cover most of it, some elegant Ruby patterns simply stop being expressible. And until iyi compiles itself, it is built by Crystal.

## About Crystal

I want to be precise here, because it is the question I get most.

iyi is not an attempt to replace Crystal, and it is not a fork left to drift. The same compiler binary builds your `.cr` files. `iyi build --crystal` gives an iyi program Crystal's standard library, `require`, and every shard you already depend on. A Kemal application compiles unchanged. Your code did not move, and it did not stop working.

What iyi adds is a rule about what a module is allowed to know. That rule is not a criticism of Crystal's design. Crystal chose openness, and that choice is why its standard library feels the way it does. I spent a decade on the receiving end of that decision and I would not trade the language for anything. iyi is what I found when I asked what those same ergonomics cost if you decide the boundary first.

The compiler is built on Crystal's, and that is recorded where it belongs, in the licence and in NOTICE.md. `iyi --version` prints `iyi 0.20.0 (built on Crystal 1.22.0-dev)`. Language first, provenance second.

## What I learned

Three things, and none of them are about compilers.

**Measure before you design.** I nearly shipped a language built on a hypothesis that a single census bucket invalidated. The correction took an hour. The wrong design would have taken a year.

**The hard constraint is usually the thing you are least willing to give up.** Open classes were not on my list of problems. They were on my list of reasons Crystal is good. Accepting that they had to go was the entire design.

**Port something real before you write a specification.** Every genuinely new requirement came out of porting Kemal's router, not out of thinking about language design.

## Try it

```sh
curl -fsSL https://raw.githubusercontent.com/iyilang/iyi/master/install.sh | sh
~/.local/bin/iyi run ~/.local/share/iyi/samples/hello.iyi
```

Linux x86-64, macOS arm64, and Windows x86-64 through `install.ps1`.

- The language: [iyi.dev](https://iyi.dev)
- Playground and tour: [iyi.dev/playground](https://iyi.dev/playground/)
- Source: [github.com/iyilang/iyi](https://github.com/iyilang/iyi)
- Packages: [packs.iyi.dev](https://packs.iyi.dev)
- Design and measurements: [SPEC.md](https://github.com/iyilang/iyi/blob/master/SPEC.md)

As of November 2026, iyi is version 0.20.0. It is young, it has rough edges, and some of it is not finished. But the edit loop is 0.13 seconds, and I have been waiting ten years to type that sentence.