Reusable workflow: dependabot-label

How to use from another repository

1) In your repo, create .github/workflows/dependabot-label.yml with:

name: Label Dependabot PRs (minor/patch)
on:
  pull_request_target:
    types: [opened, reopened, synchronize]

permissions:
  contents: read
  pull-requests: write

jobs:
  label:
    uses: navikt/helsearbeidsgiver-frontend-workflows/.github/workflows/dependabot-label-reusable.yml@<ref>
    with:
      only-dependabot: true
      label-name: automerge:delayed
      label-color: FFFF00
      label-description: Auto-merge Dependabot after delay
      include-update-types: version-update:semver-minor,version-update:semver-patch
      also-add-update-type-label: true
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}

Notes
- Place dependabot-label-reusable.yml in your shared repo at .github/workflows.
- The workflow uses dependabot/fetch-metadata to detect update type and actions/github-script to add labels.
- It auto-creates label-name if missing; it will not auto-create update type labels.
- If you want to run it for non-Dependabot PRs, set with.only-dependabot: false.
