---
title: Membership Changes
weight: 6
---

# Membership Changes

// TODO

## Rough Notes

- Dynamically add/remove nodes from cluster
- Replicate configuration changes through Raft log
- Joint consensus (C_old,new) to prevent split brain during transitions
- Or single-server changes (simpler, safer)
- AddServer and RemoveServer operations
- Catch up new servers before adding to cluster
- Handle leader stepping down if removed from cluster

### Cluster Management API

1. POST /cluster/join

Join a cluster.

```markdown
POST /cluster/join
Body:
  {
    "leader": ":<port>"    # the leader's address
  }
Response:
  - 200 OK: Successfully joined cluster
```

2. POST /cluster/leave

Leave the cluster.

```markdown
POST /cluster/leave
Response:
  - 200 OK: Successfully initiated removal from cluster
```
