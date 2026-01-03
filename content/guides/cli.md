---
title: CLI
weight: 1
---

# CLI

## Installation

### `go install`

```console
$ go install github.com/st3v3nmw/lsfr/cmd/lsfr@latest
```

This installs `lsfr` to your `$GOPATH/bin` directory. Make sure it's in your `$PATH`.

You can also install a specific version using tags (see [available versions](https://github.com/st3v3nmw/lsfr/tags)):

```console
$ go install github.com/st3v3nmw/lsfr/cmd/lsfr@v0.1.0
```

#### Update

```console
$ go install github.com/st3v3nmw/lsfr/cmd/lsfr@latest
```

### Homebrew

```console
$ brew tap st3v3nmw/tap
$ brew install st3v3nmw/tap/lsfr
```

#### Update

```console
$ brew upgrade st3v3nmw/tap/lsfr
```

### Verify Installation

```console
$ lsfr list
Available challenges:

  kv-store             - Distributed Key-Value Store (8 stages)

Start with: lsfr init <challenge-name>
```

## Quick Start

```console
$ lsfr init kv-store    # Create challenge in current directory
$ lsfr test             # Test your implementation
$ lsfr next             # Advance to next stage
```

Edit `run.sh` to launch your implementation, then run `lsfr test` to get feedback.

> [!NOTE]
> Commands support short aliases for faster typing:
> - `lsfr i` → `lsfr init`
> - `lsfr t` → `lsfr test`
> - `lsfr n` → `lsfr next`
> - `lsfr s` → `lsfr status`
> - `lsfr l` or `lsfr ls` → `lsfr list`

## Basic Workflow

### 1. Start a Challenge

```console
$ lsfr init <challenge> [path]
```

Creates a new challenge directory with:
- `run.sh` - Script that builds and runs your implementation
- `README.md` - Challenge overview and requirements
- `lsfr.yaml` - Tracks your progress
- `.gitignore` - Ignores `.lsfr/` working directory (server files and logs)

**Examples:**

```console
$ lsfr init kv-store           # Create in current directory
$ lsfr init kv-store my-kvs    # Create in ./my-kvs
```

### 2. Implement & Test

Edit `run.sh` to start your implementation.
The script must launch your server and pass through any arguments from `lsfr`:

```bash
#!/bin/bash -e

# Go
exec go run ./cmd/server "$@"

# Python
# exec python main.py "$@"

# Rust
# cargo build --release && exec ./target/release/kvstore "$@"

# Node.js
# exec node server.js "$@"
```

Then test:

```console
$ lsfr test
```

**When tests pass:**
```
PASSED ✓

Run 'lsfr next' to advance to the next stage.
```

**When tests fail:**
```
FAILED ✗

PUT http://127.0.0.1:45123/kv/ "foo"
  Expected: "key cannot be empty"
  Actual: ""

  Your server accepted an empty key when it should reject it.
  Add validation to return 400 Bad Request for empty keys.

Read the guide: lsfr.io/kv-store/http-api
```

Fix the issues, then run `lsfr test` again. The CLI is designed for quick iteration, just keep running `lsfr test` as you make changes.

### 3. Progress Through Stages

```console
$ lsfr next
```

Advances to the next stage after verifying the current stage passes. Updates `lsfr.yaml` automatically.

If the current stage hasn't been completed, `lsfr next` runs tests first and only advances if they pass.

If you're on GitHub, consider adding `lsfr` and `lsfr-<language>` (e.g., `lsfr-go`, `lsfr-rust`) as topics to your repository to share your implementation.

## Commands Reference

Run `lsfr --help` to see all available commands, or `lsfr <command> --help` for command-specific options.

### lsfr init

**Usage:** `lsfr init <challenge> [path]`

Creates a new challenge in the specified directory (or current directory if not specified).

### lsfr test

**Usage:** `lsfr test [stage]`

Runs tests for the current stage (from `lsfr.yaml`) or a specific stage if provided.

```console
$ lsfr test                # Test current stage
$ lsfr test persistence    # Test specific stage
```

### lsfr next

**Usage:** `lsfr next`

Advances to the next stage after verifying current stage passes all tests.

### lsfr status

**Usage:** `lsfr status`

Shows challenge progress and next steps:

```console
$ lsfr status
Distributed Key-Value Store

Build a distributed key-value store from scratch using the Raft consensus algorithm.

Progress:
✓ http-api           - Store and Retrieve Data
✓ persistence        - Data Survives SIGTERM
✓ crash-recovery     - Data Survives SIGKILL
→ leader-election    - Cluster Elects and Maintains Leader
  log-replication    - Data Replicates to All Nodes
  membership-changes - Add and Remove Nodes Dynamically
  fault-tolerance    - Cluster Survives Failures and Partitions
  log-compaction     - System Manages Log Growth

Read the guide: lsfr.io/kv-store/leader-election

Implement leader-election, then run 'lsfr test'.
```

### lsfr list

**Usage:** `lsfr list`

Lists all available challenges with stage counts.

## Understanding lsfr.yaml

The config file tracks your progress:

```yaml
challenge: kv-store
stages:
  current: persistence
  completed:
    - http-api
```

You can edit this manually to jump between stages or reset progress. You can also use `lsfr test <stage>` to test any stage without changing your current progress.
