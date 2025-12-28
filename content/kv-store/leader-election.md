---
title: Leader Election
weight: 4
---

# Leader Election

// TODO

## Rough Notes

- Form static 3-node cluster (--peers flag with comma-separated addresses, or API)
- Implement Raft leader election (Section 5.2 of Raft paper)
- Server states: follower, candidate, leader
- Terms as logical clock
- RequestVote RPC
- Election timeouts (randomized 150-300ms)
- Leader heartbeats (AppendEntries RPC, empty for now)
- Split vote handling
- Only leader accepts client requests, followers return 503
- Persist currentTerm and votedFor to survive crashes
