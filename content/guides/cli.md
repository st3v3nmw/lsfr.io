---
title: CLI
weight: 1
---

# CLI

## Installation

### go install

```console
$ go install github.com/st3v3nmw/lsfr/cmd/lsfr@latest
```

This installs `lsfr` to your `$GOPATH/bin` directory. Make sure it's in your `$PATH`.

### Verify Installation

```console
$ lsfr list
Available challenges:

  kv-store           - Distributed Key-Value Store (8 stages)

Start with: lsfr new <challenge-name>
```

### Next Steps

Start your first challenge:

```console
$ lsfr new kv-store
$ cd kv-store
$ lsfr test
```
