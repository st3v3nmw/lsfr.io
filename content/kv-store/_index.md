---
title: Distributed Key-Value Store
weight: 2
bookCollapseSection: true
---

# Distributed Key-Value Store Challenge

Welcome to the distributed key-value store challenge!

You'll build a distributed key-value store from scratch using Raft consensus, the same algorithm that powers [etcd](https://etcd.io/) and [Consul](https://developer.hashicorp.com/consul). Start with a single-node system that handles persistence and crash recovery, then implement leader election, log replication, and fault tolerance.

This is the first project in _lsfr_'s distributed systems series. It teaches consensus-based replication; later projects will teach different patterns like leaderless replication, CRDTs, and Byzantine fault tolerance.

## Stages

1. [HTTP API](/kv-store/http-api/)

Build a basic in-memory key-value store with GET/PUT/DELETE operations over HTTP.

2. [Persistence](/kv-store/persistence/)

Add persistence to your store. Data should survive clean shutdowns (SIGTERM).

3. [Crash Recovery](/kv-store/crash-recovery/)

Ensure data consistency after crashes. Data should survive unclean shutdowns (SIGKILL).

4. [Leader Election](/kv-store/leader-election/)

Form a cluster and elect a leader using the Raft consensus algorithm.

5. [Log Replication](/kv-store/log-replication/)

Replicate operations from the leader to followers with strong consistency guarantees.

6. [Membership Changes](/kv-store/membership-changes/)

Dynamically add and remove nodes from the cluster without downtime.

7. [Fault Tolerance](/kv-store/fault-tolerance/)

Handle node failures and network partitions while maintaining safety guarantees.

8. [Log Compaction](/kv-store/log-compaction/)

Prevent unbounded log growth through snapshots and log truncation.

## Getting Started

If you haven't already, read [this overview](/how-lsfr-works/) on how _lsfr_ works and then start with [stage 1 (HTTP API)](/kv-store/http-api/).

## Resources

### Books

- [Designing Data-Intensive Applications](https://dataintensive.net/) by Martin Kleppmann
- [Database Internals](https://www.databass.dev/) by Alex Petrov

### Papers

- [The Raft Paper](https://raft.github.io/raft.pdf) by Diego Ongaro & John Ousterhout

### Videos

- [Distributed Systems lecture series](https://www.youtube.com/playlist?list=PLeKd45zvjcDFUEv_ohr_HdUFe97RItdiB) by Martin Kleppmann

### Implementations

- [little-key-value](https://github.com/st3v3nmw/little-key-value) in Go by [@st3v3nmw](https://github.com/st3v3nmw)
