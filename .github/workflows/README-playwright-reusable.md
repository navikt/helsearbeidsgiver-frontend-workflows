# Reusable workflow: Playwright tests

This workflow runs Playwright end-to-end tests with sharding and merges HTML reports. It’s designed to be called from other repositories via `workflow_call` and supports pinning the Playwright container version.

## Inputs
- `playwright-version` (string, default `v1.55.0-jammy`)
  - Container tag for `mcr.microsoft.com/playwright:<tag>`.
- `node-version` (string, default `lts/*`)
  - Node.js version for actions/setup-node.
- `matrix` (string, default `{"shardIndex":[1,2,3,4],"shardTotal":[4]}`)
  - JSON string for test sharding. Adjust to change shard count or indexes.

## Secrets
- `npm_reader_token` (optional)
  - Token for GitHub Packages registry if your project pulls packages from `npm.pkg.github.com`.

## Artifacts
- Per-shard blob reports: `blob-report-<index>`
- Merged HTML report: `html-report--attempt-<run_attempt>`

## Usage (from another repository, shared workflows repo)
Example caller workflow (`.github/workflows/playwright.yml` in the consumer repo):

```yaml
name: Playwright Tests
on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

permissions:
  contents: read
  pull-requests: write

jobs:
  e2e:
    uses: navikt/helsearbeidsgiver-frontend-workflows/.github/workflows/playwright-reusable.yml@<ref>
    with:
      playwright-version: v1.55.0-jammy
      node-version: lts/*
      matrix: '{"shardIndex":[1,2,3,4],"shardTotal":[4]}'
    secrets:
      npm_reader_token: ${{ secrets.READER_TOKEN }}
```

Replace `<owner>/<shared-repo>@<ref>` with your shared workflows repository and ref/tag.

## Usage (calling this repo’s reusable workflow)
You can also call this workflow as-is from another repo using this repository as the source:

```yaml
jobs:
  e2e:
    uses: navikt/spinntektsmelding-frontend/.github/workflows/playwright-reusable.yml@main
    with:
      playwright-version: v1.55.0-jammy
      matrix: '{"shardIndex":[1,2,3,4],"shardTotal":[4]}'
```

## Notes
- Ensure `playwright-version` matches your project’s Playwright dependency to avoid runtime mismatches.
- Adjust `matrix` to change sharding. For no sharding, use `{"shardIndex":[1],"shardTotal":[1]}`.
- If your org requires a PAT for the packages registry, pass it via `secrets.npm_reader_token`.
