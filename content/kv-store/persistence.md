---
title: Persistence
weight: 2
---

# Persistence

In this stage, you'll add persistence to your key-value store.
Data should survive clean shutdowns and be restored when the server restarts.

## Clean Shutdown

When your server receives a SIGTERM signal, it should:

1. Save all key-value pairs to disk
2. Exit gracefully

## Startup Recovery

When your server starts, it should:

1. Check the data directory for existing data
2. Load any previously saved key-value pairs
3. Continue serving requests with the restored data

If no previous data exists, start with an empty store.

## Storage

You can store the data however you choose; the implementation is up to you.

Some approaches to consider:

**Storage Strategy:**

- Snapshot on shutdown: Save all data after receiving SIGTERM
- Continuous persistence: Save changes as they happen

**Data Structures:**

- Simple formats: JSON & binary serialization, plain text files, etc
- Tree-based: B-trees, Log-structured merge-trees (LSM trees), etc

Your server must accept a `--data-dir` flag that specifies where to persist data:

```console
$ ./run.sh --port 8080 --data-dir /tmp/tmpzz5vkl5d
```
