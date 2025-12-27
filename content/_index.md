---
title: Home
---

# Learn by Building Real Systems

You know that feeling when you read about Raft consensus or consistent hashing and think _"okay, but how would I actually implement this?"_ That gap between understanding concepts and building working systems is what _lsfr_ is for.

## Project-Based Learning

Learn by building complete, real-world systems rather than doing isolated exercises. You'll implement actual distributed databases, message queues, and compilers that mirror production systems.

```bash
$ lsfr new kv-store
Created challenge in current directory.
  run.sh       - Builds and runs your implementation
  README.md    - Challenge overview and requirements
  lsfr.yaml    - Tracks your progress

Implement http-api stage, then run 'lsfr test'.
```

## Build Incrementally

Each stage introduces one new concept only after you've solidified the previous one. You can use any language you want to build it! Go, Rust, Python, or whatever you prefer.

The tests verify your system works by running it and checking behavior, not implementation details, so you can focus on learning the concepts without wrestling with complex setup.

```bash
$ lsfr test
Testing http-api: HTTP API with GET/PUT/DELETE Operations

✓ PUT Basic Operations
✓ PUT Edge and Error Cases
✓ GET Basic Operations
✓ GET Edge and Error Cases
✓ DELETE Basic Operations
✓ DELETE Edge and Error Cases
✓ CLEAR Operations
✓ Concurrent Operations - Different Keys
✓ Concurrent Operations - Same Key
✓ Check Allowed HTTP Methods

PASSED ✓

Run 'lsfr next' to advance to the next stage.
```

When you're ready, advance to the next stage:

```bash
$ lsfr next
Advanced to persistence: Data Persistence

Your system must survive restarts and handle concurrent writes.

Read the guide: lsfr.io/kv-store/persistence

Run 'lsfr test' when ready.
```

## Open-Source by Default

All tests, tooling, reference implementations, and this website are open source. Check out [_lsfr's_ source code](https://github.com/st3v3nmw/lsfr) and this [website's source code](https://github.com/st3v3nmw/lsfr.io).

```bash
$ lsfr list
Available challenges:

  kv-store             - Distributed Key-Value Store (8 stages)

Start with: lsfr new <challenge-name>
```

## Ready to Build?

[Start with the distributed key-value store challenge](/kv-store). Or [learn how lsfr works](/how-lsfr-works) if you want to understand the tooling first.
