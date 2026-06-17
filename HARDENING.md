<!-- markdownlint-disable -->

# Hardening Report: kiegroup--git-backporting/v4.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **kiegroup--git-backporting/v4.9.0** was hardened automatically. 0 finding(s) were identified and resolved across 1 iteration(s).

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed examples/on-pr-merge/automated-workflow.yaml: (1) Pinned kiegroup/kie-ci/.ci/actions/parse-labels@main to full SHA 1ef5227ca436f22d1f78684bf0ea0e391b91923a with # main comment. (2) Pinned actions/checkout@v4 to full SHA 34e114876b0b11c390a56381ad16ebd13914f8d5 with # v4 comment. (3) Added top-level permissions block with contents: write and pull-requests: write — the minimum needed for a backporting workflow that pushes branches and opens pull requests.

