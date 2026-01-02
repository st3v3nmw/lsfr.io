---
title: How lsfr Works
weight: 1
---

# How _lsfr_ Works

Reading about how systems work is one thing. Building them is another.

_lsfr_ breaks down complex systems into stages you can actually implement. Build a distributed database, write a compiler, or create a message queue from scratch. Each challenge starts simple and adds complexity one stage at a time.

You write real code that actually works. Tests verify your implementation handles the hard problems: network failures, crash recovery, concurrent access, etc. By the end, you understand these systems because you've built them yourself.

## Challenges

Each challenge breaks down a complex system into manageable stages that can be built incrementally. For instance, the [distributed key-value store challenge](/kv-store/) starts with a simple in-memory store with a HTTP API and by the end, you have a sharded distributed key-value store.

Each stage comes with tests that simulate real-world scenarios like network failures and crash recovery. The tests verify your system's behavior, not implementation details, so you're free to choose your own data structures, algorithms, and approaches. These are essentially end-to-end tests that check if your system actually works.

Each stage explains what you need to implement and links out to good resources for learning more. Why reinvent the wheel when Kleppmann and others have already explained it better? 😅

Ready to try it out? Here's how to set up _lsfr_ and start your first challenge.

## Installation

See [this guide](/guides/cli/#installation) on how to install _lsfr_ on your system.

## Pick a Challenge

Start with the [distributed key-value store challenge](/kv-store/); it's a great introduction to distributed systems concepts. Check the challenge's page for details on the first stage.

### Scaffolding

Run `lsfr new kv-store` to create a new challenge directory with:

- `run.sh` - Builds and runs your implementation
- `README.md` - Challenge overview and requirements
- `lsfr.yaml` - Tracks your progress

Update `run.sh` with the commands to build and run your implementation. You can use _any_ language - Go, Python, Rust, even Ponylang - as long as `run.sh` can start your program and pass through any command-line arguments from `lsfr test`.

## Implement & Test

Write your implementation in any language to solve the challenge's first stage. When ready, run `lsfr test` to verify your solution works correctly. The tests focus on behavior, not implementation details.

## Advance Through Stages

Pass the current stage, then run `lsfr next` to unlock the next stage. Each stage builds on the previous one, gradually adding complexity.

By the end of the challenge, you'll have a deep understanding of how these systems actually work because you built one yourself.

If you're on GitHub, consider adding `lsfr` and `lsfr-<language>` (e.g., `lsfr-go`, `lsfr-rust`) as topics to your repository to share your implementation.

Good luck! 🚀
