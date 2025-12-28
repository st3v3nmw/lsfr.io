---
title: Log Compaction
weight: 8
---

# Log Compaction

// TODO

## Rough Notes

- Prevent unbounded log growth
- Take snapshots of state machine (KV store)
- Truncate log after snapshot
- InstallSnapshot RPC for slow/catching-up followers
- Snapshot metadata (last included index, last included term)
- Incremental snapshots or full state dump
- When to snapshot (configurable: every N entries, every M seconds, size threshold)
- Note: snapshot size = data size problem (document limitation, point to storage engine solutions)
