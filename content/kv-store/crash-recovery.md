---
title: Crash Recovery
weight: 3
---

# Crash Recovery

Your server currently saves data on clean shutdown but loses everything if it crashes. In this stage, you'll add durability so data survives unexpected failures.

## Write-Ahead Logging

Implement a [Write-Ahead Log (WAL)](https://www.architecture-weekly.com/p/the-write-ahead-log-a-foundation) that records operations before they're applied to memory. Each write operation must be written to the log file before updating your in-memory store.

### Log Format

Your log should record operations in append-only fashion. The format is up to you - [JSONL](https://jsonlines.org/) (one JSON object per line), binary serialization, or plain text all work.

Each log entry needs enough information to replay the operation:

- Operation type (e.g., "set", "delete", "clear")
- Key
- Value
- Any other metadata you need for replay

### Durability

After appending an operation to the log, ensure it's physically written to disk before responding to the client. Use your language's file sync mechanism (`fsync`, `flush`, etc.) to force the operating system to persist the write.

Without sync, the OS may buffer writes in memory and you'll lose data on crash.

> [!WARNING]
> Syncing on every operation is slow since you're forcing a disk write and blocking the response. This is the correct trade-off for durability, but it limits throughput. Production databases use techniques like batching to amortize the fsync cost across multiple operations.

## Recovery Procedure

When your server starts:

1. Load the most recent snapshot (from the persistence stage) if one exists
2. Replay all operations from the WAL that occurred after the snapshot
3. Resume serving requests

If no snapshot exists, replay the entire log from the beginning.

### Handling Corrupted Logs

The log file may contain partial writes at the end if the server crashed mid-write. Your replay logic should handle this gracefully:

- Skip incomplete/corrupted entries at the end of the log
- Process all valid entries before the corruption
- Continue serving requests with the recovered data

## Checkpointing

As your log grows, replaying from the beginning becomes slow. Periodically create snapshots of your in-memory state and truncate the log.

When to checkpoint is up to you - after N operations, every M seconds, when the log reaches a certain size, etc. The test doesn't care about your checkpoint strategy, only that recovery works correctly.

After creating a snapshot:

1. Write the snapshot to a new file
2. Truncate or create a new WAL file
3. Continue logging operations

On recovery, load the latest snapshot and replay only the operations logged after that snapshot.

## Storage Layout

You now have two types of files:

- **Snapshot**: Full state at a point in time (from previous stage)
- **WAL**: Operations logged since the last snapshot

Organize these in the working directory however makes sense - separate files, subdirectories, naming conventions, etc. The test only cares that recovery works, not how you structure the files.

## Testing

Your server will be started with the working directory:

```console
$ ./run.sh --port 8080 --working-dir .lsfr/run-20251226-210357
```

Your server will be tested with unexpected crashes:

```console
$ lsfr test crash-recovery
Testing crash-recovery: Crash Recovery with Write-Ahead Logging

✓ Basic WAL Durability
✓ Multiple Crash Recovery Cycles
✓ Rapid Write Burst Before Crash
✓ Large Dataset With Concurrent Writes

PASSED ✓

Run 'lsfr next' to advance to the next stage.
```

The test will:

1. Store data in your server
2. Kill the server process (SIGKILL) without warning
3. Restart your server
4. Verify all data that was acknowledged before the crash is still present
