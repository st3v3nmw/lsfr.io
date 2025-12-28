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
