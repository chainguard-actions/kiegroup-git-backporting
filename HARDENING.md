<!-- markdownlint-disable -->

# Hardening Report: kiegroup--git-backporting/v4.8.6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **kiegroup--git-backporting/v4.8.6** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): `${{ github.event.inputs.options }}` is interpolated directly inside a `run:` shell command string. A workflow_dispatch caller can supply arbitrary shell metacharacters (e.g. `; malicious-command`) that will be executed by the runner. Offending line: `run: npm run release:prepare -- --ci --no-git.commit ${{ github.event.inputs.options }}`

Locations:

- `.github/workflows/prepare-release.yml:38`

### script-injection (severity: high)

Rule (a): `${{ github.event.inputs.options }}` is interpolated directly inside a `run:` shell command string. A workflow_dispatch caller can supply arbitrary shell metacharacters that will be executed by the runner. Offending line: `run: npm run release -- --ci --no-increment --no-git.commit ${{ github.event.inputs.options }}`

Locations:

- `.github/workflows/release.yml:38`

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the tag is moved. Failing references: `actions/checkout@v4`, `actions/setup-node@v4` (ci.yml); `actions/checkout@v4`, `ArtiomTr/jest-coverage-report-action@v2` (coverage.yml); `actions/checkout@v4`, `actions/setup-node@v4`, `gr2m/create-or-update-pull-request-action@v1.x` (prepare-release.yml); `actions/checkout@v4`, `actions/setup-node@v4` (pull-request.yml); `actions/checkout@v4`, `actions/setup-node@v4` (release.yml).

Locations:

- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:22`
- `.github/workflows/coverage.yml:14`
- `.github/workflows/coverage.yml:15`
- `.github/workflows/prepare-release.yml:21`
- `.github/workflows/prepare-release.yml:25`
- `.github/workflows/prepare-release.yml:40`
- `.github/workflows/pull-request.yml:21`
- `.github/workflows/pull-request.yml:23`
- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:25`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no job within them defines job-level permissions either. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/coverage.yml:1`
- `.github/workflows/prepare-release.yml:1`
- `.github/workflows/pull-request.yml:1`
- `.github/workflows/release.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all 3 finding types across 5 workflow files:

1. **script-injection** (prepare-release.yml, release.yml): Moved `${{ github.event.inputs.options }}` out of `run:` shell strings into `env:` blocks as `RELEASE_OPTIONS`. Used `${RELEASE_OPTIONS:+"$RELEASE_OPTIONS"}` so the optional argument drops out entirely when empty, preventing shell injection while preserving correct argument-count behavior.

2. **unpinned-uses**: Pinned all action references to full 40-char SHAs with tag comments:
   - `actions/checkout@v4` → `@34e114876b0b11c390a56381ad16ebd13914f8d5 # v4`
   - `actions/setup-node@v4` → `@49933ea5288caeca8642d1e84afbd3f7d6820020 # v4`
   - `ArtiomTr/jest-coverage-report-action@v2` → `@7f750dd50f5585533321eb7ebc482b936b49a5d4 # v2`
   - `gr2m/create-or-update-pull-request-action@v1.x` → `@483e2e5c8e68c420e72687127cfdc30a45254be7 # v1.x`

3. **missing-permissions**: Added top-level `permissions:` blocks to all 5 workflow files with least-privilege scopes:
   - ci.yml: `contents: read`
   - coverage.yml: `contents: read`, `pull-requests: write`, `checks: write`
   - prepare-release.yml: `contents: write`, `pull-requests: write`
   - pull-request.yml: `contents: read`
   - release.yml: `contents: write`

