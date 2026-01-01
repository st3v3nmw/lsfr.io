---
title: Testing
weight: 1
---

# Testing Framework

The lsfr testing framework (`attest`) provides a fluent API for writing black-box tests against programs. Tests validate external behavior without accessing implementation internals.

## Quick Start

A typical test suite:

```go
package kvstore

import (
    . "github.com/st3v3nmw/lsfr/internal/attest"
)

func HTTPAPI() *Suite {
    return New().
        // 0
        Setup(func(do *Do) {
            do.Start("node")
        }).

        // 1
        Test("PUT stores data", func(do *Do) {
            do.HTTP("node", "PUT", "/kv/key", "value").T().
                Status(Is(200)).
                Assert("Your server should accept PUT requests.\n" +
                    "Ensure your HTTP handler processes PUT to /kv/{key}.")
        }).

        // 2
        Test("GET retrieves data", func(do *Do) {
            do.HTTP("node", "GET", "/kv/key").T().
                Status(Is(200)).
                Body(Is("value")).
                Assert("Your server should return stored values.")
        })
}
```

Tests run sequentially. State persists between tests so data written in test 1 is available in test 2. First failure stops execution.

## HTTP Assertions

Make HTTP requests and validate responses:

```go
// Basic request
do.HTTP("node", "GET", "/kv/key").T().
    Status(Is(200)).
    Body(Is("value")).
    Assert("Your server should return stored values.")

// With body
do.HTTP("node", "PUT", "/kv/key", "value").T().
    Status(Is(200)).
    Assert("Your server should accept PUT requests.")

// With custom headers
do.HTTP("node", "POST", "/api", `{"key":"value"}`, H{"Content-Type": "application/json"}).T().
    Status(Is(201)).
    Assert(...)

// Status only
do.HTTP("node", "DELETE", "/kv/key").T().
    Status(Is(200)).
    Assert("Your server should accept DELETE requests.")

// JSON fields
do.HTTP("node", "GET", "/cluster/info").T().
    Status(Is(200)).
    JSON("role", Is("follower")).
    JSON("term", Is("1")).
    Assert("Should return cluster info")

do.HTTP("node", "GET", "/log").T().
    Status(Is(200)).
    JSON("entries.0.term", Is("1")).
    JSON("entries.1.index", Is("1")).
    Assert("Should return log entries")

do.HTTP("node", "GET", "/cluster/info").T().
    Status(Is(200)).
    JSON("leader", IsNull[string]()).
    Assert("Leader should be null when no leader elected")
```

## CLI Assertions

Execute CLI commands and validate output:

```go
// Check exit code and output
do.Exec("--help").T().
    ExitCode(Is(0)).
    Output(Contains("Usage:")).
    Assert("Your command should show usage information.")

// Check exit code only
do.Exec("invalid", "args").T().
    ExitCode(Is(1)).
    Assert("Your command should reject invalid arguments.")
```

## Checkers

### `Is(value)`

Exact equality:

```go
.Status(Is(200))
.Body(Is("key not found\n"))
.ExitCode(Is(0))
```

### `IsNull[T]()`

Checks if a value is null:

```go
// Check for null JSON field
.JSON("leader", IsNull[string]())
```

Requires a type parameter to specify the expected field type.

### `Contains(substring)`

String contains:

```go
.Body(Contains("error"))
.Output(Contains("Usage:"))
```

### `Matches(pattern)`

Regex matching:

```go
.Body(Matches(`^[0-9]+$`))
.Output(Matches(`version \d+\.\d+\.\d+`))
```

### `HasLen[T](length)`

Validates that a value has a specific length. Works on arrays, slices, maps, channels, and strings:

```go
// String body length
.Body(HasLen[string](5))

// JSON array length
.JSON("items", HasLen[string](3))

// JSON string field length
.JSON("name", HasLen[string](5))

// Empty arrays
.JSON("items", HasLen[string](0))
```

Requires a type parameter to specify the field type being checked.

### `OneOf(values...)`

Match any of several values:

```go
// Useful for concurrent operations where order is non-deterministic
.Body(OneOf("value1", "value2", "value3"))
```

### `Not(checker)`

Negates another checker:

```go
.Status(Not(Is(500)))
.Body(Not(Contains("panic")))
```

### Multiple Checkers

Chain multiple checkers for the same field:

```go
// Multiple status checks
.Status(Is(200), Not(Is(404)), Not(Is(500)))

// Multiple body checks
.Body(Contains("Hello"), Contains("World"), Not(Contains("Goodbye")))

// Multiple JSON field checks
JSON("role", Is("leader"), Not(Is("follower")), Not(Is("candidate")))
```

All checkers for a field must pass. If any checker fails, the assertion fails.

## Timing

### `Eventually()`

Retry until condition becomes true or timeout (default 5s):

```go
// Wait for replica to sync
do.HTTP("replica", "GET", "/kv/key").
    Eventually().T().
    Status(Is(200)).
    Body(Is("value")).
    Assert("Replica should eventually receive replicated data.")

// Custom timeout
do.HTTP("replica", "GET", "/kv/key").
    Eventually().Within(10 * time.Second).T().
    Status(Is(200)).
    Body(Is("value")).
    Assert("Replica should sync within 10 seconds.")
```

### `Consistently()`

Verify condition stays true for duration (default 5s):

```go
// Verify value remains stable
do.HTTP("node", "GET", "/kv/key").
    Consistently().T().
    Status(Is(200)).
    Body(Is("value")).
    Assert("Value should remain stable.")

// Custom duration
do.HTTP("node", "GET", "/kv/key").
    Consistently().For(2 * time.Second).T().
    Status(Is(200)).
    Body(Is("value")).
    Assert("Value should remain stable for 2 seconds.")
```

### Default (Immediate)

Without `Eventually()` or `Consistently()`, checks execute once immediately:

```go
do.HTTP("node", "GET", "/kv/key").T().
    Status(Is(200)).
    Assert("Your server should return the value immediately.")
```

## Service Management

### `Start(name, args...)`

Start a service with auto-assigned port:

```go
do.Start("node")
do.Start("replica", "--seed=123")
```

Port is auto-assigned by the OS. Services receive `--port` and `--working-dir` flags automatically. Each test run gets an isolated working directory like `run-20240115-143022`.

### `Stop(name)`

Graceful shutdown with SIGTERM:

```go
do.Stop("node")
```

Sends SIGTERM and waits for graceful exit. If process doesn't exit within timeout, sends SIGKILL.

### `Kill(name)`

Immediate termination with SIGKILL:

```go
do.Kill("node")
```

### `Restart(name, sig...)`

Restart a service:

```go
// Graceful restart (SIGTERM)
do.Restart("node")

// Crash simulation (SIGKILL)
do.Restart("node", syscall.SIGKILL)
```

The optional signal parameter controls how the process is stopped before restart. SIGKILL simulates crashes with no cleanup, SIGTERM allows graceful shutdown.

## Concurrency

### `Concurrently(funcs...)`

Run operations in parallel:

```go
do.Concurrently(
    func() {
        do.HTTP("node", "PUT", "/kv/key1", "value1").T().
            Status(Is(200)).
            Assert(...)
    },
    func() {
        do.HTTP("node", "PUT", "/kv/key2", "value2").T().
            Status(Is(200)).
            Assert(...)
    },
)

// Verify both succeeded
do.HTTP("node", "GET", "/kv/key1").T().
    Status(Is(200)).
    Body(Is("value1")).
    Assert(...)
do.HTTP("node", "GET", "/kv/key2").T().
    Status(Is(200)).
    Body(Is("value2")).
    Assert(...)
```

Waits for all functions to complete. If any panic, the first panic is re-raised after all complete.

## Writing Good Assertions

Assertion messages appear when tests fail. They should help developers fix the problem.

Good assertion messages describe expected behavior, provide concrete next steps, and reference relevant concepts:

```go
Assert("Your server should reject empty keys.\n" +
    "Add validation to return 400 Bad Request for empty keys.")

Assert("Your server should preserve data across crashes.\n" +
    "Implement a Write-Ahead Log (WAL) that records operations before applying them.\n" +
    "Ensure writes are durably stored before acknowledging to the client.")
```

Focus on requirements rather than implementation details. Say "Ensure writes are durably stored before acknowledging" instead of "You must call fsync".

Avoid:

- Generic messages: "Fix your code"
- Vague messages: "This is wrong"
- Past tense: "Your server accepted an empty key when it should reject it"
- Unnecessary adverbs: "Your server should handle requests correctly"

Example error output:

```console
PUT /kv/ "value"
  Expected response: "key cannot be empty\n"
  Actual response: ""

  Your server should reject empty keys.
  Add validation to return 400 Bad Request for empty keys.
```

## Creating a Challenge

### Directory Structure

```
challenges/
└── kvstore/
    ├── init.go           # Challenge registration
    ├── http_api.go       # Stage 1
    ├── persistence.go    # Stage 2
    └── crash_recovery.go # Stage 3
```

### Stage Structure

Each stage is a function returning `*Suite`:

```go
package kvstore

import (
    . "github.com/st3v3nmw/lsfr/internal/attest"
)

func HTTPAPI() *Suite {
    return New().
        // 0
        Setup(func(do *Do) {
            do.Start("node")
        }).

        // 1
        Test("PUT Basic Operations", func(do *Do) {
            do.HTTP("node", "PUT", "/kv/key", "value").T().
                Status(Is(200)).
                Assert("Your server should accept PUT requests.")
        }).

        // 2
        Test("GET Basic Operations", func(do *Do) {
            do.HTTP("node", "GET", "/kv/key").T().
                Status(Is(200)).
                Body(Is("value")).
                Assert("Your server should return stored values.")
        })
}
```

Import `attest` with `.` for cleaner syntax. Number tests with comments (0, 1, 2...) to visually separate tests so that they're not crowded.

### Challenge Registration

Create `init.go`:

```go
package kvstore

import "github.com/st3v3nmw/lsfr/internal/registry"

func init() {
    challenge := &registry.Challenge{
        Name: "Distributed Key-Value Store",
        Summary: `Build a distributed key-value store from scratch.
You'll start with a simple HTTP API and progressively add persistence,
crash recovery, clustering, replication, and consensus.`,
    }

    challenge.AddStage("http-api", "Store and Retrieve Data", HTTPAPI)
    challenge.AddStage("persistence", "Data Survives SIGTERM", Persistence)
    challenge.AddStage("crash-recovery", "Data Survives SIGKILL", CrashRecovery)
    challenge.AddStage("leader-election", "Cluster Elects and Maintains Leader", LeaderElection)

    registry.RegisterChallenge("kv-store", challenge)
}
```

### Auto-Discovery

Import your challenge in `challenges/challenges.go`:

```go
package challenges

import (
    _ "github.com/st3v3nmw/lsfr/challenges/kvstore"
)
```
