<!-- markdownlint-disable -->

# Hardening Report: kvankova--code-embedder/v1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **kvankova--code-embedder/v1.1.1** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable version tags instead of full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if the tag is moved to a malicious commit. Affected references: pr-title.yml: `amannn/action-semantic-pull-request@v5`; release-drafter.yml: `release-drafter/release-drafter@v6`; release.yml: `actions/checkout@v3`; run_precommit_pytest.yml: `actions/checkout@v2`, `actions/setup-python@v2`, `abatilo/actions-poetry@v2`, `actions/cache@v3`.

Locations:

- `.github/workflows/pr-title.yml:12`
- `.github/workflows/release-drafter.yml:13`
- `.github/workflows/release.yml:10`
- `.github/workflows/run_precommit_pytest.yml:10`
- `.github/workflows/run_precommit_pytest.yml:13`
- `.github/workflows/run_precommit_pytest.yml:17`
- `.github/workflows/run_precommit_pytest.yml:22`

### missing-permissions (severity: medium)

Two workflow files have no top-level `permissions:` key and no job-level `permissions:` key on any job. Without explicit permissions, workflows run with the default (potentially broad) token permissions. `release.yml` and `run_precommit_pytest.yml` both lack any permissions declaration.

Locations:

- `.github/workflows/release.yml:1`
- `.github/workflows/run_precommit_pytest.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Pinned all 7 unpinned action references to full 40-character SHA hashes with original tags preserved as comments. Added top-level permissions blocks to release.yml (contents: write, needed for git push) and run_precommit_pytest.yml (contents: read, minimal for checkout/test workflows). pr-title.yml and release-drafter.yml already had permissions blocks and only needed action pinning.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed all 5 unquoted $TAG shell variable expansions in .github/workflows/release.yml. The TAG variable was already correctly placed in the env: block for each step (not directly interpolated from ${{ }} expressions in run: scripts), but the expansions were unquoted. Changed all occurrences from $TAG to ${TAG} within double-quoted sed patterns and git commit message strings, preventing shell metacharacter injection.

