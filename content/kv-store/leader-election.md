---
title: Leader Election
weight: 4
---

# Leader Election

{{< katex />}}

Implement [Raft](https://raft.github.io/raft.pdf) leader election to form a static 5-node cluster that elects a single leader.

## Cluster Formation

Start nodes with the `--peers` flag specifying all cluster members:

```console
$ ./run.sh --port 8001 --working-dir .lsfr/run-T --peers=:8001,:8002,:8003,:8004,:8005
```

The `--peers` list includes all cluster members, including this node itself, and as such, all nodes receive the same `--peers` list.

The cluster is static; membership doesn't change in this stage.

## Leader Election

All nodes start as followers. If a follower doesn't receive heartbeats within the **election timeout** (randomized between **500-1,000ms**), it becomes a candidate and starts an election.

Candidates request votes from other nodes. A candidate becomes leader if it receives votes from a majority ($\lceil (n+1)/2 \rceil$ where $n$ is cluster size; 3 votes for a 5-node cluster). Each node grants at most one vote per term.

Terms act as a logical clock. When a node discovers a higher term, it immediately updates its term and reverts to follower.

## Heartbeats

Leaders send `AppendEntries` RPC heartbeats every **100ms** to maintain authority. The `entries` array is empty in this stage.

If a follower doesn't receive heartbeats within the election timeout, it starts a new election.

> [!NOTE]
> These timing values are more conservative than the Raft paper's suggested 150-300ms election timeout. The larger ratio (5-10x) between heartbeat interval and election timeout accounts for varying execution speeds across different implementation languages and prevents spurious elections caused by garbage collection pauses or interpreter overhead.

## Client Requests

Leaders accept GET/PUT/DELETE requests from the HTTP API stage.

Followers respond with `307 Temporary Redirect` and `Location` header pointing to the current leader:

```
HTTP/1.1 307 Temporary Redirect
Location: http://127.0.0.1:8001/kv/mykey
```

If no leader is known, return `503 Service Unavailable`.

## Persistence

Persist `currentTerm` and `votedFor` to disk before responding to `RequestVote` RPC. Use `fsync` to ensure durability.

State must survive crashes and restarts. A node that crashes and restarts should resume with its persisted term and vote.

## API

### Raft RPCs

1. POST /raft/request-vote

`RequestVote` RPC - invoked by candidates to gather votes during leader election.

```markdown
POST /raft/request-vote
Body:
  {
    "term": <int>,                # candidate's term
    "candidate-id": ":<port>",    # candidate requesting vote
    "last-log-index": <int>,      # index of candidate's last log entry
    "last-log-term": <int>        # term of candidate's last log entry
  }
Response:
  - 200 OK:
    {
      "term": <int>,            # current-term, for candidate to update itself
      "vote-granted": <bool>    # true means candidate received vote
    }
```

In this stage with no log entries yet, set `last-log-index` and `last-log-term` to `0`.

2. POST /raft/append-entries

`AppendEntries` RPC - used for heartbeats (empty entries) to maintain leader authority.

```markdown
POST /raft/append-entries
Body:
  {
    "term": <int>,              # leader's term
    "leader-id": ":<port>",     # so follower can redirect clients
    "prev-log-index": <int>,    # index of log entry immediately preceding new ones
    "prev-log-term": <int>,     # term of prev-log-index entry
    "entries": [],              # empty for heartbeats; will contain log entries later
    "leader-commit": <int>      # leader's commit-index
  }
Response:
  - 200 OK:
    {
      "term": <int>,       # current-term, for leader to update itself
      "success": <bool>    # true if follower contained entry matching prev-log-index/prev-log-term
    }
```

In this stage with no log entries yet, set `prev-log-index` and `prev-log-term` to `0`.

### Testing APIs

The endpoints are _lsfr_-specific for testing and observability.

3. GET /cluster/info

Get the node's current cluster state and role.

```markdown
GET /cluster/info
Response:
  - 200 OK:
    {
      "role": "leader|candidate|follower",
      "term": <int>,
      "leader": ":<port>",         # null if no known leader
      "voted-for": ":<port>",      # null if haven't voted this term
                                   # own address if voted for self
      "peers": [":<port>", ...]    # all cluster members, sorted lexicographically
    }
```

Returns a snapshot of the node's state at the instant the request is made.
The `leader` field is `null` when no leader is known (startup, during election, after leader failure).

4. POST /cluster/partition

Simulate a network partition by blocking Raft RPC communication with specific peers.

```markdown
POST /cluster/partition
Body:
  {
    "peers": [":<port>", ":<port>"]    # nodes this node can communicate with
                                       # empty list = isolated from all nodes
  }
Response:
  - 200 OK: Partition applied successfully
```

Bidirectionally blocks `/raft/*` RPC endpoints between this node and all nodes NOT in the `peers` list.
Takes effect immediately; in-flight RPCs complete. `/kv/*` client requests and `/cluster/*` testing end points are not affected.

Partition state persists across crashes and restarts.
A node that crashes and restarts while partitioned restarts with the partition still active.

5. POST /cluster/heal

Restore full network connectivity, removing any active partitions.

```markdown
POST /cluster/heal
Response:
  - 200 OK: Network connectivity restored
```

Removes all partition restrictions immediately. Idempotent (safe to call when not partitioned).
Heal state persists across crashes and restarts.

## Testing

Your server will be started as a 5-node cluster:

```console
$ ./run.sh --port 8001 --working-dir .lsfr/run-T --peers=:8001,:8002,:8003,:8004,:8005
```

The tests will verify leader election behavior:

```console
$ lsfr test leader-election
Testing leader-election: Cluster Elects and Maintains Leader

✓ Leader Election Completes
✓ Exactly One Leader Per Term
✓ Leader Maintains Authority via Heartbeats
✓ Follower Redirects Clients to Leader
✓ State Survives Crashes
✓ Minority Partition Cannot Elect Leader
✓ Majority Partition Elects Leader
✓ Healing After Partition

PASSED ✓

Run 'lsfr next' to advance to the next stage.
```

Example failure:

```console
$ lsfr test
Testing leader-election: Cluster Elects and Maintains Leader

✓ Leader Election Completes
✓ Exactly One Leader Per Term
✓ Leader Maintains Authority via Heartbeats
✓ Follower Redirects Clients to Leader
✓ State Survives Crashes
✗ Minority Partition Cannot Elect Leader

GET http://127.0.0.1:8000/cluster/info
  Expected: No leader elected in minority partition (2 nodes) after 5 seconds
  Actual: Node :8001 became leader in minority partition

  Are you requiring majority votes?

FAILED ✗

Read the guide: lsfr.io/kv-store/leader-election
```

## Resources

- [Raft Visualization](https://thesecretlivesofdata.com/raft/) by The Secret Lives of Data
- [The Raft Consensus Algorithm](https://raft.github.io/)
- [Distributed Systems 6.2: Raft](https://www.youtube.com/watch?v=uXEYuDwm7e4) by Martin Kleppmann
- [Students' Guide to Raft](https://thesquareplanet.com/blog/students-guide-to-raft/) by Jon Gjengset
