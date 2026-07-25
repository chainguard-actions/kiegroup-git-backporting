<!-- markdownlint-disable -->

# Hardening Report: kiegroup--git-backporting/v4.10.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **kiegroup--git-backporting/v4.10.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files use tag-based or branch-based `uses:` references instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the referenced action tag is moved or compromised.

Failing references:
- ci.yml: `actions/checkout@v6`, `actions/setup-node@v6`
- coverage.yml: `actions/checkout@v6`, `actions/setup-node@v6`, `ArtiomTr/jest-coverage-report-action@v2`
- prepare-release.yml: `actions/checkout@v6`, `actions/setup-node@v6`, `gr2m/create-or-update-pull-request-action@v1.x`
- pull-request.yml: `actions/checkout@v6`, `actions/setup-node@v6` (×2 jobs)
- release.yml: `actions/checkout@v6`, `actions/setup-node@v6`

Locations:

- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:21`
- `.github/workflows/coverage.yml:14`
- `.github/workflows/coverage.yml:15`
- `.github/workflows/coverage.yml:18`
- `.github/workflows/prepare-release.yml:21`
- `.github/workflows/prepare-release.yml:24`
- `.github/workflows/prepare-release.yml:40`
- `.github/workflows/pull-request.yml:19`
- `.github/workflows/pull-request.yml:21`
- `.github/workflows/pull-request.yml:33`
- `.github/workflows/pull-request.yml:35`
- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`

### script-injection (severity: high)

Sub-rule (a): `github.event.inputs.options` is interpolated directly inside a `run:` shell command string. This is a `workflow_dispatch` user-controlled input that is injected into the shell before quoting, allowing an attacker with dispatch access to inject arbitrary shell commands.

Offending lines:
- prepare-release.yml: `run: npm run release:prepare -- --ci --no-git.commit ${{ github.event.inputs.options }}`
- release.yml: `run: npm run release -- --ci --no-increment --no-git.commit ${{ github.event.inputs.options }}`

Fix: move the input into an env var and quote it: `env:\n  OPTIONS: ${{ github.event.inputs.options }}\nrun: npm run release:prepare -- --ci --no-git.commit "$OPTIONS"`

Locations:

- `.github/workflows/prepare-release.yml:38`
- `.github/workflows/release.yml:35`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no job within any workflow defines a job-level `permissions:` key. Without explicit permissions, workflows run with the default repository permissions (which can be `write` for contents, packages, etc.), violating the principle of least privilege. This is especially concerning for `coverage.yml` which uses the `pull_request_target` trigger (which runs with write permissions on the base repo even for PRs from forks).

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/coverage.yml:1`
- `.github/workflows/prepare-release.yml:1`
- `.github/workflows/pull-request.yml:1`
- `.github/workflows/release.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three finding types across all five workflow files:

1. unpinned-uses: Pinned all action references to full 40-char SHAs with tag comments preserved:
   - actions/checkout@v6 → @d23441a48e516b6c34aea4fa41551a30e30af803 # v6
   - actions/setup-node@v6 → @249970729cb0ef3589644e2896645e5dc5ba9c38 # v6
   - ArtiomTr/jest-coverage-report-action@v2 → @7f750dd50f5585533321eb7ebc482b936b49a5d4 # v2
   - gr2m/create-or-update-pull-request-action@v1.x → @483e2e5c8e68c420e72687127cfdc30a45254be7 # v1.x

2. script-injection: Moved ${{ github.event.inputs.options }} out of run: shell strings in prepare-release.yml and release.yml into env: blocks as OPTIONS, and used ${OPTIONS:+"$OPTIONS"} to safely pass the optional argument (drops out entirely when empty, preventing an empty positional argument).

3. missing-permissions: Added top-level permissions blocks to all five workflow files with minimal required permissions: ci.yml and pull-request.yml get contents: read; coverage.yml gets contents: read + pull-requests: write; prepare-release.yml gets contents: write + pull-requests: write; release.yml gets contents: write + id-token: write.

