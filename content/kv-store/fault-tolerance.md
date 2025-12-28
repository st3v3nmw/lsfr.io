---
title: Fault Tolerance
weight: 7
---

# Fault Tolerance

// TODO

## Rough Notes

- Handle leader crashes (followers detect timeout, elect new leader)
- Handle follower crashes (leader retries AppendEntries)
- Log repair on recovery (conflicting entries, missing entries)
- Network partitions (majority partition continues, minority blocks)
- Safety: committed entries never lost
- Log consistency checks (prevLogIndex, prevLogTerm)
- Truncate conflicting uncommitted entries
- Leader completeness property
