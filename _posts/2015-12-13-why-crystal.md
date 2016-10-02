---
status: publish
published: true
title: Why Crystal?
date: '2015-12-13 13:42'
tags:
  - crystal
  - ruby
  - performance
  - api
---

**Spoiler**: This is an opinionated post about programming languages.

I'm a Rubyist, i love Ruby, the community, the productivity and lots more.
For more than 4 years i professionally write Ruby to get paid and i really like to keep it that way but
i'm also aware that languages/tools are destined to be replaced.

In 2015 we've seen lots of blog posts starting like 'How we moved from Ruby to X ..' . Well that's the living
proof of people looking for better alternatives. We shouldn't take these as 'Ruby is not good'. Quite the contrary we should be aware
of that 'X'.

In most of those the 'X' is Go, Rust, Elixir etc.. ***I hereby claim that:*** As a Rubyist that that 'X' should be nothing else than ***[Crystal](http://crystal-lang.org)***.

You'd ask. Well Serdar:

>Why Crystal?

And i'd answer with something like:

>Learning a language takes days but becoming proficient and productive enough takes years.

Crystal is %90 Ruby with

- Similar syntax
- Same idioms

**Plus**

- Compiled
- Native code
- Superb performance

and much more.

## Enter Crystal

Let's start with an example

`fib.rb`

```ruby
def fibonacci(n)
  return n if n <= 1
  fibonacci(n - 1) + fibonacci(n - 2)
end
puts fibonacci 40
```

This is how you'd do ***fibonacci*** (without memoization) in Crystal. Did you notice that the file extension `.rb`? Well at the same time this is Ruby code :)

Let's run to see it working on Ruby and Crystal.

```
ruby fib.rb
102334155

crystal fib.rb
102334155
```

Awesome. Let's take a look at the time taken.

**Ruby**

```
time ruby fib.rb
ruby fib.rb  16.62s user 0.08s system 99% cpu 16.805 total
```

**Crystal**

```
time crystal fib.rb
crystal fib.rb  0.85s user 0.18s system 118% cpu 0.870 total
```

Wow! That's pretty awesome. We practically did nothing and gained **20x** performance.

But wait what if we turn on **[LLVM](http://llvm.org/)** optimizations

```
crystal build --release fib.rb
time ./fib
./fib  0.67s user 0.00s system 99% cpu 0.678 total
```

***[Native code](https://en.wikipedia.org/wiki/Machine_code)*** rocks. We build the Crystal program with all the LLVM optimizations and
run the generated native code in this case `./fib` and now we are nearly **25x** faster than Ruby :)

That's a really simple demonstration and a big reason of why that ***'X'*** should be ***Crystal***.

As a web developer i was pretty curious to see how a web framework similar to **Sinatra** would benefit from this
performance. So i created **[Kemal](http://www.github.com/sdogruyol/kemal)**.

## Enter Kemal

To understand the true potential of **[Crystal](http://crystal-lang.org)** and make something useful i thought that something like **[Sinatra](http://www.sinatrarb.com)** would be awesome
thus **[Kemal](http://www.github.com/sdogruyol/kemal)** is born.

It's literally as simple as Sinatra.

```ruby
require "kemal"

get "/" do
  "Hello World!"
end
```

but with a big performance difference :)

```wrk -c 100 -d 20 http://localhost:3000```

- Kemal (Production) - **64986 requests per second** with an average time of **170μs**
- Sinatra (Thin) - **2274 requests per second** with an average time of **43.82ms**

Currently Kemal is under development and is not yet feature complete but has the following

- Support all REST verbs
- Request/Response context, easy parameter handling
- Middlewares
- Built-in JSON support
- Built-in static file serving
- Built-in view templating via `ecr`

To see what you can build with **[Kemal](http://www.github.com/sdogruyol/kemal)** you better check **[Kamber](http://github.com/f/kamber)**

## Current status

Crystal is in alpha stage and the latest stable version is **0.9.1**.

Most of the standard library is complete and stable. It's not yet production-ready but
you can use Crystal today.

A big thumbs up for Crystal is that it's written in Crystal. You can easily read the source
code of **[Crystal](https://github.com/manastech/crystal)**  and contribute back it by opening pull requests / issues.

## Community

The community is really nice :) It follows the roots of **[MINASWAN](https://en.wikipedia.org/wiki/MINASWAN)** and currently
has an irc channel (#crystal-lang on freenode) and [a mailing group](https://groups.google.com/forum/?fromgroups#!forum/crystal-lang).

## Closing Thoughts

If you're reading this far then i'm thankful for your time :) I really hope that you enjoy using **Crystal** soon. You can ask me any
questions and reach me at [@sdogruyol](http://twitter.com/sdogruyol)

Happy Crystaling <3

**P.S:** You can support **Crystal** development with this [fundraiser](https://www.bountysource.com/teams/crystal-lang/fundraiser).
