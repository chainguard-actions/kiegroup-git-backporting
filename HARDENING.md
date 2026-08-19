<!-- markdownlint-disable -->

# Hardening Report: kiegroup--git-backporting/v4.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **kiegroup--git-backporting/v4.9.0** was hardened automatically. 12 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The expression `${{ github.event.inputs.options }}` is directly interpolated inside a `run:` shell command string. A `workflow_dispatch` caller can supply shell metacharacters (`;`, `|`, `&`, `$(...)`, etc.) in the `options` input, achieving arbitrary command execution on the runner. The offending line is: `run: npm run release:prepare -- --ci --no-git.commit ${{ github.event.inputs.options }}`. Fix: move the value into an env var and double-quote it in the shell, e.g. `env: OPTIONS: ${{ github.event.inputs.options }}` then `run: npm run release:prepare -- --ci --no-git.commit "$OPTIONS"`.

Locations:

- `.github/workflows/prepare-release.yml:38`

### script-injection (severity: high)

Sub-rule (a): The expression `${{ github.event.inputs.options }}` is directly interpolated inside a `run:` shell command string. A `workflow_dispatch` caller can supply shell metacharacters in the `options` input, achieving arbitrary command execution on the runner. The offending line is: `run: npm run release -- --ci --no-increment --no-git.commit ${{ github.event.inputs.options }}`. Fix: move the value into an env var and double-quote it in the shell.

Locations:

- `.github/workflows/release.yml:30`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN is granted its default (often broad) permissions, violating the principle of least privilege. Add a top-level `permissions: {}` block and grant only the specific scopes required (e.g. `contents: read`).

Locations:

- `.github/workflows/ci.yml:1`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. This workflow uses `pull_request_target` (which has write access to the base repo), making the absence of explicit permissions especially risky. Add a minimal `permissions:` block.

Locations:

- `.github/workflows/coverage.yml:1`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN is granted its default (often broad) permissions. Add a minimal `permissions:` block.

Locations:

- `.github/workflows/prepare-release.yml:1`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN is granted its default (often broad) permissions. Add a minimal `permissions:` block.

Locations:

- `.github/workflows/pull-request.yml:1`

### missing-permissions (severity: medium)

The workflow file has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the GITHUB_TOKEN is granted its default (often broad) permissions. Add a minimal `permissions:` block.

Locations:

- `.github/workflows/release.yml:1`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable tag refs instead of immutable 40-character commit SHAs, making the workflow vulnerable to supply-chain attacks if the referenced tag is moved or the repository is compromised. Failing references: `actions/checkout@v6` (line 21), `actions/setup-node@v6` (line 23). Pin each to a full SHA, e.g. `actions/checkout@<40-hex-sha> # v6`.

Locations:

- `.github/workflows/ci.yml:21`
- `.github/workflows/ci.yml:23`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable tag refs instead of immutable 40-character commit SHAs. Failing references: `actions/checkout@v4` (line 17), `ArtiomTr/jest-coverage-report-action@v2` (line 18). Pin each to a full SHA.

Locations:

- `.github/workflows/coverage.yml:17`
- `.github/workflows/coverage.yml:18`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable tag refs instead of immutable 40-character commit SHAs. Failing references: `actions/checkout@v4` (line 21), `actions/setup-node@v6` (line 24), `gr2m/create-or-update-pull-request-action@v1.x` (line 39). Pin each to a full SHA.

Locations:

- `.github/workflows/prepare-release.yml:21`
- `.github/workflows/prepare-release.yml:24`
- `.github/workflows/prepare-release.yml:39`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable tag refs instead of immutable 40-character commit SHAs. Failing references: `actions/checkout@v6` (line 22), `actions/setup-node@v6` (line 24). Pin each to a full SHA.

Locations:

- `.github/workflows/pull-request.yml:22`
- `.github/workflows/pull-request.yml:24`

### unpinned-uses (severity: high)

All `uses:` references in this workflow use mutable tag refs instead of immutable 40-character commit SHAs. Failing references: `actions/checkout@v6` (line 20), `actions/setup-node@v6` (line 23). Pin each to a full SHA.

Locations:

- `.github/workflows/release.yml:20`
- `.github/workflows/release.yml:23`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, missing-permissions, unpinned-uses

**Notes:**

Fixed all 12 findings across 5 workflow files:

1. script-injection (2 findings): Moved `${{ github.event.inputs.options }}` into `env: OPTIONS:` blocks in prepare-release.yml and release.yml. Used `${OPTIONS:+"$OPTIONS"}` in shell commands to safely handle the optional empty-default input without passing an empty argument.

2. missing-permissions (5 findings): Added minimal `permissions:` blocks to all 5 workflow files: ci.yml (`{}`), coverage.yml (`contents: read, pull-requests: write`), prepare-release.yml (`contents: write, pull-requests: write`), pull-request.yml (`contents: read`), release.yml (`contents: write`).

3. unpinned-uses (5 findings, 10 action references): Pinned all action `uses:` references to full 40-char commit SHAs with tag comments: actions/checkout@v6→d23441a4, actions/setup-node@v6→24997072, actions/checkout@v4→11d5960a, ArtiomTr/jest-coverage-report-action@v2→7f750dd5, gr2m/create-or-update-pull-request-action@v1.x→483e2e5c.

