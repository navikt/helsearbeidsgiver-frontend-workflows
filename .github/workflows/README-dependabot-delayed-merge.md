Reusable workflow: dependabot-delayed-merge

How to use from another repository

1) In your repo, create .github/workflows/dependabot-delayed-merge.yml with:

name: Merge Dependabot PRs after delay
on:
  schedule:
    - cron: '0 6 * * 1-5' # weekdays 06:00 UTC
  workflow_dispatch:

permissions:
  contents: write
  pull-requests: write

jobs:
  merge:
    uses: navikt/helsearbeidsgiver-frontend-workflows/.github/workflows/dependabot-delayed-merge-reusable.yml@<ref>
    with:
      only-actor: dependabot[bot]
      label-name: automerge:delayed
      days-delay: 5
      merge-method: squash
      allow-draft: false
      require-clean-mergeable-state: true
      dry-run: false
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}

Notes
- Place dependabot-delayed-merge-reusable.yml in your shared repo at .github/workflows.
- Schedule determines when merges are attempted; you can add workflow_dispatch for manual runs.
- Set dry-run: true to log candidates without merging.
- If your repos require a PAT, provide it via secrets.token; otherwise, GITHUB_TOKEN suffices.
