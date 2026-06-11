---
title: "cryload 5.0.0: The CI-First HTTP Load Tester That Actually Fails Your Builds"
date: 2026-06-11 10:47:00 +0300
tags: [crystal, cryload, load-testing, ci-cd, devops]
---

When you spend years building and optimizing high-performance backend infrastructure like the [Kemal web framework](https://kemalcr.com), load testing becomes second nature. You want to know exactly how your service behaves under pressure.

But over time, I noticed a recurring friction point in the modern Developer Experience (DX): *Automated load testing in CI/CD pipelines is painfully primitive*.

We have fantastic tools like `ab`, `wrk`, `hey`, and `oha`. They are incredibly fast and provide beautiful terminal outputs. However, the moment you try to integrate them into a GitHub Action or a GitLab CI pipeline to prevent performance regressions, things get ugly. You end up writing brittle `bash` scripts and complex regex patterns just to extract the `p99` latency from standard output, all to answer a simple question: *"Did this PR make my app slower?"*

I wanted a tool that enforces SLAs natively. That’s why I built [cryload](https://github.com/sdogruyol/cryload).

## Enter cryload: Load Testing Built for Pipelines

`cryload` is a single-binary HTTP load testing CLI written in [Crystal](https://crystal-lang.org). It gives you the raw performance and low resource footprint you expect from native code, but with one major philosophical difference: It is designed to fail your build.

Instead of just printing pretty numbers, `cryload` allows you to set explicit `SLA gates` directly via flags.

Let's say your service must keep its p99 latency under 200ms and its failure rate below 1%. You simply run:

```bash
cryload http://localhost:3000/api -n 1000 --max-p99 200 --max-fail-rate 1
```

If your service breaches these limits, `cryload` throws an `Exit Code 1`. Your CI pipeline fails immediately. No wrapper scripts, no text parsing. Just a straightforward SLA enforcement.

(For those who still want to chart trends over time, it also spits out structured JSON or CSV reports).

## What’s New in 5.0.0?
Today, I'm thrilled to announce cryload 5.0.0, which brings massive improvements to accuracy, performance, and real-world testing scenarios.

### 1. Honest Percentiles (HDR-style Histogram)
In standard load testing tools, transport errors like timeouts or connection drops often pollute the latency statistics, giving you a skewed view of your application's actual performance. In 5.0.0, we fixed the math. I implemented a new HDR-style histogram that ensures `p50` through `p999` remain dead accurate (within 1%) from microseconds up to minutes. Transport errors are now tracked completely separately from successful request latency.

### 2. Massive Performance Wins (Up to 13x Faster)
Thanks to a completely refactored connection pooling architecture that now operates on a per-origin basis, testing multiple URLs or running cache-busting scenarios is significantly faster. In some multi-URL benchmarks, `cryload` 5.0.0 is up to 13x faster than previous iterations.

### 3. Real-World Connection Testing
We introduced two highly requested flags to simulate complex, real-world traffic:

- `--disable-keepalive`: Want to measure the actual cost of TLS handshakes and TCP connection setup on every single request? This flag forces a fresh connection every time.

- `--body-stdin`: You can now pipe request bodies straight into `cryload`. This is incredibly useful for dynamic payload generation:

```bash
jq -c '.payload' fix.json | cryload http://localhost:3000/api --body-stdin
```

## Getting Started

Since `cryload` is written in [Crystal](https://crystal-lang.org), it compiles down to native code and ships as a single, dependency-free binary for Linux, macOS, and Windows.

You can install it with a single `curl` command or grab the latest release directly from the repository.

[🔗 Check out the cryload repository on GitHub](https://github.com/sdogruyol/cryload)

I’d love for you to drop it into your CI pipelines and put your infrastructure to the test. If you manage to break it, issues and PRs are, as always, very welcome.

Happy benchmarking!
