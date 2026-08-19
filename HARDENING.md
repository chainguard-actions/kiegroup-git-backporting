<!-- markdownlint-disable -->

# Hardening Report: kiegroup--git-backporting/v4.8.7

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **kiegroup--git-backporting/v4.8.7** was hardened automatically. 12 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): `${{ github.event.inputs.options }}` is interpolated directly inside a `run:` shell command. This is a user-controlled `workflow_dispatch` input that flows through YAML template substitution before the shell sees it, enabling arbitrary command injection. Offending line: `run: npm run release:prepare -- --ci --no-git.commit ${{ github.event.inputs.options }}`

Locations:

- `.github/workflows/prepare-release.yml:38`

### script-injection (severity: high)

Rule (a): `${{ github.event.inputs.options }}` is interpolated directly inside a `run:` shell command. This is a user-controlled `workflow_dispatch` input that flows through YAML template substitution before the shell sees it, enabling arbitrary command injection. Offending line: `run: npm run release -- --ci --no-increment --no-git.commit ${{ github.event.inputs.options }}`

Locations:

- `.github/workflows/release.yml:35`

### unpinned-uses (severity: high)

Multiple `uses:` references use mutable tag refs instead of pinned 40-character SHA commits, making the workflow vulnerable to supply-chain attacks if the referenced tag is moved. Unpinned refs: `actions/checkout@v4`, `actions/setup-node@v4`.

Locations:

- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:20`

### unpinned-uses (severity: high)

Multiple `uses:` references use mutable tag refs instead of pinned 40-character SHA commits. Unpinned refs: `actions/checkout@v4`, `ArtiomTr/jest-coverage-report-action@v2`.

Locations:

- `.github/workflows/coverage.yml:14`
- `.github/workflows/coverage.yml:15`

### unpinned-uses (severity: high)

Multiple `uses:` references use mutable tag refs instead of pinned 40-character SHA commits. Unpinned refs: `actions/checkout@v4`, `actions/setup-node@v4`, `gr2m/create-or-update-pull-request-action@v1.x`.

Locations:

- `.github/workflows/prepare-release.yml:22`
- `.github/workflows/prepare-release.yml:25`
- `.github/workflows/prepare-release.yml:39`

### unpinned-uses (severity: high)

Multiple `uses:` references use mutable tag refs instead of pinned 40-character SHA commits. Unpinned refs: `actions/checkout@v4`, `actions/setup-node@v4`.

Locations:

- `.github/workflows/pull-request.yml:19`
- `.github/workflows/pull-request.yml:21`

### unpinned-uses (severity: high)

Multiple `uses:` references use mutable tag refs instead of pinned 40-character SHA commits. Unpinned refs: `actions/checkout@v4`, `actions/setup-node@v4`.

Locations:

- `.github/workflows/release.yml:21`
- `.github/workflows/release.yml:24`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (write-all by default for many repositories).

Locations:

- `.github/workflows/ci.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. This workflow uses `pull_request_target` (which runs with write access to the base repo), making missing permissions especially risky.

Locations:

- `.github/workflows/coverage.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions.

Locations:

- `.github/workflows/prepare-release.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions.

Locations:

- `.github/workflows/pull-request.yml:1`

### missing-permissions (severity: medium)

The workflow has no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, the workflow inherits the repository's default token permissions.

Locations:

- `.github/workflows/release.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all 12 findings across 5 workflow files:

1. script-injection (2 findings): Moved `${{ github.event.inputs.options }}` from inline `run:` shell commands into `env:` blocks as `RELEASE_OPTIONS`, then used bash arrays to safely expand the options in prepare-release.yml and release.yml.

2. unpinned-uses (5 findings): Pinned all action references to full 40-char SHAs:
   - actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262
   - actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020
   - ArtiomTr/jest-coverage-report-action@v2 → @7f750dd50f5585533321eb7ebc482b936b49a5d4
   - gr2m/create-or-update-pull-request-action@v1.x → @483e2e5c8e68c420e72687127cfdc30a45254be7

3. missing-permissions (5 findings): Added explicit minimal permissions blocks to all 5 workflow files (ci.yml: contents:read; coverage.yml: contents:read + pull-requests:write + checks:write; prepare-release.yml: contents:write + pull-requests:write; pull-request.yml: contents:read; release.yml: contents:write + id-token:write).

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection vulnerabilities where the user-controlled `$RELEASE_OPTIONS` env var (sourced from `github.event.inputs.options`) was expanded unquoted inside bash array constructions. Changed `args+=($RELEASE_OPTIONS)` to `args+=("$RELEASE_OPTIONS")` in both `.github/workflows/prepare-release.yml` (line 42) and `.github/workflows/release.yml` (line 35). The double-quoting prevents word-splitting and glob expansion, ensuring the value is treated as a single argument rather than allowing shell metacharacter injection.

### Iteration 3

**Fixes applied:** script-injection

**Notes:**

Fixed unquoted $NPM_TOKEN shell expansion in two workflow files:
1. .github/workflows/prepare-release.yml (line 36): changed `$NPM_TOKEN` to `"$NPM_TOKEN"`
2. .github/workflows/release.yml (line 30): changed `$NPM_TOKEN` to `"$NPM_TOKEN"`

Both instances of `npm config set //registry.npmjs.org/:_authToken $NPM_TOKEN` now properly quote the variable to prevent shell metacharacter interpretation.

