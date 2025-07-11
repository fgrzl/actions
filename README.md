# fgrzl/actions
Reusable GitHub Actions for Go projects and repository automation.

## Overview
This repository provides reusable GitHub Actions for common automation tasks in Go-based repositories, including tagging commits, notifying the Go proxy, pruning old pre-release tags, and cleaning up workflow runs.

## Available Actions
* **`tag-commit`** – Tags a commit with a specified version.
* **`notify-go-proxy`** – Notifies the Go proxy of a new version tag.
* **`prune-tags`** – Deletes old pre-release tags from the repository.
* **`workflow-cleanup`** – Removes outdated workflow runs to keep your history clean.

## Example: Pre-Release Workflow
```yaml
name: Pre-Release

on:
  workflow_dispatch:

permissions:
  contents: write
  packages: read

jobs:
  TagAndPublish:
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
        uses: fgrzl/actions/prune-tags@v1
```

## Action Inputs
### `tag-commit`

| Input | Description           | Required |
| ----- | --------------------- | -------- |
| `tag` | Version tag to create | ✅        |

### `notify-go-proxy`

| Input | Description           | Required |
| ----- | --------------------- | -------- |
| `tag` | Version tag to notify | ✅        |

### `prune-tags`
No inputs required.

### `workflow-cleanup`
| Input      | Description                                        | Required | Default |
| ---------- | -------------------------------------------------- | -------- | ------- |
| `days_old` | Age threshold for deleting workflow runs (in days) | ❌        | `7`     |
| `per_page` | Runs fetched per page (max 100)                    | ❌        | `50`    |

## Example: Workflow Cleanup
```yaml
name: Clean Up Old Workflow Runs

on:
  schedule:
    - cron: '0 0 * * *' # Daily at midnight UTC
  workflow_dispatch:

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
      - name: Delete Old Workflow Runs
        uses: fgrzl/actions/workflow-cleanup@v1
        with:
          days_old: 14
          per_page: 100
```
