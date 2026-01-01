---
title: Log Replication
weight: 5
---

# Log Replication

// TODO

## Rough Notes

- Raft log replaces the WAL from stage 3
- Leader appends client operations to log, replicates to followers
- AppendEntries RPC with actual log entries
- Commit index (entry committed when replicated to majority)
- Apply committed entries to state machine (KV store)
- Log matching property (consistency across nodes)
- Fsync log entries before responding to client
- nextIndex and matchIndex tracking per follower
- Happy path only - no crashes/failures yet

## Resources

- [The Raft Consensus Algorithm](https://raft.github.io/)
- [Distributed Systems 6.2: Raft](https://www.youtube.com/watch?v=uXEYuDwm7e4) by Martin Kleppmann
- [Students' Guide to Raft](https://thesquareplanet.com/blog/students-guide-to-raft/) by Jon Gjengset
- [Database Internals Chapter 14: Consensus](https://www.databass.dev/) by Alex Petrov
- [Designing Data-Intensive Applications Chapter 5: Replication](https://dataintensive.net/) by Martin Kleppmann
