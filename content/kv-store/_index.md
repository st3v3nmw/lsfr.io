---
title: Distributed Key-Value Store
weight: 2
bookCollapseSection: true
---

# Distributed Key-Value Store Challenge

Welcome to the distributed key-value store challenge!

In this challenge, you'll build a distributed key-value store from scratch.
You'll start with a simple HTTP API and progressively add persistence, crash recovery, clustering, replication, and consensus mechanisms.

By the end, you'll have built a system that can handle node failures, network partitions, and scale across multiple nodes while maintaining data consistency.

## Stages

1. [HTTP API](/kv-store/http-api)

Build a basic in-memory key-value store with GET/PUT/DELETE operations over HTTP.

2. [Persistence](/kv-store/persistence)

Add durability to your store. Data should survive clean shutdowns (SIGTERM).

3. [Crash Recovery](/kv-store/crash-recovery)

Ensure data consistency after crashes. Data should survive unclean shutdowns (SIGKILL).

4. [Clustering](/kv-store/clustering)

Discover and connect to other nodes in a leader-follower arrangement.

5. [Read Replicas](/kv-store/read-replicas)

Add read replicas that follow the leader. Handle eventual consistency across the cluster.

6. [Fault Tolerance](/kv-store/fault-tolerance)

Handle leader failures and network partitions while maintaining cluster consistency.

7. [Strong Consistency](/kv-store/strong-consistency)

Implement strong consistency guarantees for read and write operations.

8. [Sharding](/kv-store/sharding)

Distribute data across multiple shards for horizontal scaling.

## Getting Started

If you haven't already, read [this overview](/how-lsfr-works) on how _lsfr_ works and then start with [stage 1 (HTTP API)](/kv-store/http-api).

## Resources

### Books

- [Designing Data-Intensive Applications](https://dataintensive.net/) by Martin Kleppmann
- [Database Internals](https://www.databass.dev/) by Alex Petrov

### Videos

- [Distributed Systems lecture series](https://www.youtube.com/playlist?list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB) by Martin Kleppmann

### Implementations

- [little-key-value](https://github.com/st3v3nmw/little-key-value) in Go by [@st3v3nmw](https://github.com/st3v3nmw)
