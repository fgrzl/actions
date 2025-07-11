# fgrzl/actions

Reusable GitHub Actions for Go projects and repository automation.

## Overview

This repository contains a set of reusable GitHub Actions for common repository automation tasks, including tagging commits, notifying the Go proxy, and cleaning up old pre-release tags.

## Available Actions

- **Tag Commit** (`tag-commit`): Tags a commit with a specified version.
- **Notify Go Proxy** (`notify-go-proxy`): Notifies the Go proxy of a new version tag.
- **Prune Pre-release Tags** (`prune-tags`): Cleans up old pre-release tags from the repository.

## Usage

```yaml
name: Pre-Release

on:
  workflow_dispatch:

permissions:
  contents: write
  packages: read

jobs:
  TagAndPublish:
    name: Tag and Notify Go Proxy
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Full History
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Install GitVersion
        uses: gittools/actions/gitversion/setup@v4.0.1

      - name: Calculate Version
        id: version
        uses: gittools/actions/gitversion/execute@v4.0.1

      - name: Tag Commit
        uses: fgrzl/actions/tag-commit@v1
        with:
          tag: ${{ steps.version.outputs.semVer }}

      - name: Notify Go Proxy
        uses: fgrzl/actions/notify-go-proxy@v1
        with:
          tag: ${{ steps.version.outputs.semVer }}

      - name: Prune Pre-release Tags
        uses: fgrzl/actions/cleanup-old-prerelease-tags@v1
```

### Inputs

#### tag-commit

- `tag` (required): The tag to create (e.g., `v1.2.3`).

#### notify-go-proxy

- `tag` (required): The tag to notify the Go proxy about.

#### prune-tags

No inputs required.
