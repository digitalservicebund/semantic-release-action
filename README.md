# semantic-release-action

A reusable GitHub Action that runs [semantic-release](https://github.com/semantic-release/semantic-release)
with the `commit-analyzer`, `release-notes-generator`, and `github` plugins,
using pinned package versions to protect against unexpected breaking changes.

## Usage

```yaml
name: Release

on:
  push:
    branches:
      - main

permissions:
  contents: write
  issues: write
  pull-requests: write

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Semantic Release
        uses: digitalservicebund/semantic-release-action@<ref>
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

Replace `<ref>` with a tag, branch, or commit SHA of this action.

## Inputs

| Name                               | Required | Default    | Description                                                                   |
| ----------------------------------- | -------- | ---------- | ------------------------------------------------------------------------------ |
| `github-token`                      | yes      | –          | Token used by semantic-release to create GitHub releases, tags and comments. Typically `secrets.GITHUB_TOKEN`. |
| `node-version`                      | no       | `26`       | Node.js version used to run semantic-release.                                 |
| `semantic-release-version`          | no       | `25.0.9`   | Pinned version of the `semantic-release` package.                             |
| `commit-analyzer-version`           | no       | `13.0.1`   | Pinned version of `@semantic-release/commit-analyzer`.                        |
| `release-notes-generator-version`   | no       | `14.1.1`   | Pinned version of `@semantic-release/release-notes-generator`.                |
| `github-plugin-version`             | no       | `12.0.9`   | Pinned version of `@semantic-release/github`.                                 |
| `working-directory`                 | no       | `.`        | Directory to run semantic-release in.                                         |
| `extra-args`                        | no       | `""`       | Additional arguments passed to the semantic-release CLI.                      |

## What it does

1. Checks out the repository with full history (`fetch-depth: 0`), required
   by semantic-release to analyze the full commit history.
2. Sets up the requested Node.js version.
3. Runs `semantic-release` via `npx`, installing the exact pinned versions of
   `semantic-release`, `@semantic-release/commit-analyzer`,
   `@semantic-release/release-notes-generator`, and `@semantic-release/github`.

## Why pin versions?

`npx -p <package>` without a version resolves to the latest release at
run time. Pinning to exact versions ensures releases are reproducible and
protects the calling workflow from unannounced breaking changes upstream.
`actions/checkout` and `actions/setup-node` are additionally pinned by commit
SHA for the same reason.

## Permissions

The calling workflow must grant at least:

```yaml
permissions:
  contents: write
  issues: write
  pull-requests: write
```

## Updating pinned versions

Bump the relevant `*-version` default in [`action.yaml`](./action.yaml) after
verifying the new package version against your release process.
