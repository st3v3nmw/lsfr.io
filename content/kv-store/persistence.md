---
title: Persistence
weight: 2
---

# Persistence

In this stage, you'll add persistence to your key-value store.
Data should survive clean shutdowns and be restored when the server restarts.

## Graceful Shutdown

When your server receives a SIGTERM signal, it should:

1. Wait for in-flight requests to complete (within 5 seconds)
2. Save all key-value pairs to disk
3. Exit with status code `0`

## Startup Recovery

When your server starts, it should:

1. Check the working directory for existing data
2. Load any previously saved key-value pairs
3. Continue serving requests with the restored data

If no previous data exists, start with an empty store.

## Storage

Save your in-memory state to disk during shutdown and restore it on startup. Create your data files in the working directory (passed via `--working-dir`). The serialization format and file naming are up to you - JSON, binary, plain text, whatever.

This approach survives clean shutdowns but not crashes. If the process dies unexpectedly, you'll lose any data that wasn't saved. That's fine for this stage - you'll add crash recovery in the next stage.

## Testing

Your server will be started with the working directory where it should store data:

```console
$ ./run.sh --port 8080 --working-dir .lsfr/run-20251226-210357
```

You can test your implementation using the `lsfr` command:

```console
$ lsfr test
Testing persistence: Data Survives SIGTERM

✓ Store Initial Testing Data
✓ Verify Data Survives Restart
✓ Check Data Integrity After Multiple Restarts
✓ Test Persistence When Under Load

PASSED ✓

Run 'lsfr next' to advance to the next stage.
```

The test will:

1. Store data in your server
2. Send SIGTERM to trigger graceful shutdown
3. Restart your server
4. Verify all data is still present
