---
title: CI/CD
weight: 2
---

# CI/CD

Run `lsfr` tests automatically in GitHub Actions.

## GitHub Actions

Add `.github/workflows/lsfr.yml` to your repository:

```yaml
name: lsfr Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - uses: st3v3nmw/lsfr-action@main
```

The action runs `lsfr test` on every push to main and on pull requests.

### Custom Working Directory

If your `lsfr.yaml` isn't at the repository root:

```yaml
- uses: st3v3nmw/lsfr-action@main
  with:
    working-directory: './my-challenge'
```
