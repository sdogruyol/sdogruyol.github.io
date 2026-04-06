---
title: 'Rethinking HTTP Benchmarking: Why I Built cryload with Crystal'
date: '2026-04-06 09:00'
tags:
  - crystal
  - benchmarking
  - cryload
  - performance
  - http
---

In the world of high-performance web services, knowing your limits is everything. Whether you are building a microservice in Go, a web app with [Kemal](https://kemalcr.com/) (Crystal's most popular web framework), or a high-throughput API in Rust, you eventually face the same question: How much load can this actually handle?

While the ecosystem isn't short of benchmarking tools, I often found myself wanting something that combined the simplicity of ab (Apache Benchmark) with the raw, modern power of tools like wrk—all while leveraging the unique capabilities of the Crystal programming language.

That's why I built cryload.

## The Problem with Heavy Benchmarkers

A benchmarking tool's primary job is to stress the target, not itself. However, many traditional tools suffer from high resource overhead. If your load generator is consuming 90% of your CPU just to coordinate its own threads, the results you're getting for your target service are likely skewed.

When I started designing cryload, I had one guiding principle: **Efficiency through Minimalism.**

## The Secret Sauce: Why Crystal?

Being a member of the Crystal Core Team, I've seen firsthand how the language strikes a perfect balance between developer productivity and bare-metal performance. For a tool like cryload, Crystal was the obvious choice for several reasons:

**Compiled Performance:** Crystal compiles to efficient native code via LLVM. This means cryload runs with the speed of C/C++, which is crucial when you're trying to saturate a network interface with thousands of concurrent requests.

**The Fiber Model:** Crystal's concurrency is based on Fibers (cooperative lightweight threads). This allows cryload to handle thousands of concurrent connections with a very small memory footprint. It doesn't need a heavy OS thread for every connection, making it incredibly "lean."

**Type Safety & Speed:** We get the safety of a compiled language without the boilerplate. This allowed me to iterate quickly on the HTTP engine while ensuring the core remains rock-solid under heavy load.

## cryload in action

cryload is designed to be intuitive. You don't need a 50-page manual to start benchmarking. Here are a few common scenarios:

### 1. Basic Stress Test

Run for 10 seconds with 100 concurrent connections:

```bash
cryload http://localhost:3000 -d 10 -c 100
```

### 2. Controlled Throughput

Rate-limited run at 100 requests per second total:

```bash
cryload http://localhost:3000/ -n 1000 -c 50 --rate 100
```

## Understanding the Output

One of the things I'm most proud of is the clarity of the output. Here is what a typical cryload run looks like:

```text
Preparing to make it CRY for 10 seconds with 100 connections!
Running load test @ http://localhost:3000

  Latency (ms)      avg: 583.57   stdev: 487.84   max: 2873.63

  Percentiles (ms)  p50: 414.0   p90: 1155.0   p95: 1870.0
                    p99: 2607.0   p999: 2854.0

1747 requests in 10.39s
Requests/sec:  168.14
Responses: 1747    Errors: 0
2xx: 1747    Non-2xx: 0
Status codes: 200: 1747
```

## Why These Metrics Matter

**Requests/sec (Throughput):** Tells you the raw capacity of your server.

**Latency Distribution:** The summary line gives you **avg**, **stdev**, and **max** latency in milliseconds. That is useful context, but a single average can hide how most requests actually feel.

Below that, **Percentiles (ms)** spells out the shape of the curve: **p50** is the median (half of requests are faster), **p90** and **p95** show when things start to slow for a growing slice of traffic, **p99** catches the "almost worst" cases, and **p999** zooms in on the extreme tail. Together with **max**, that ladder from p50 through p999 tells you whether you're serving a tight experience or fighting rare but painful spikes.

**Status Codes:** Crucial for spotting when your server starts "dropping the ball" (5xx errors) under heavy pressure.

## Philosophy of Throughput

In the latest iterations of cryload, I've focused on giving users more control over the "shape" of the load. While sometimes you want to "blast" a server with everything you've got, other times you need to simulate steady, sustained traffic. This philosophy of providing both raw power and granular control is what drives the project forward.

## Open Source and Moving Forward

cryload is, and will always be, Open Source. Building it in the open has allowed me to see how developers across different ecosystems use it to harden their services.

If you are looking for a benchmarking tool that is fast, lightweight, and built on modern principles, I invite you to give cryload a try. Whether you're benchmarking a small API or a massive distributed system, cryload is designed to push your code to its absolute limits.

Explore the project on GitHub: [sdogruyol/cryload](https://github.com/sdogruyol/cryload)

What are your thoughts on modern benchmarking? I'd love to hear how you test your services and what features you'd like to see in the future of cryload. Let's discuss in the comments!
