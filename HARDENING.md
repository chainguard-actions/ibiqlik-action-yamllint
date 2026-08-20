<!-- markdownlint-disable -->

# Hardening Report: ibiqlik--action-yamllint/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ibiqlik--action-yamllint/v3.1.0** was hardened automatically. 4 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The composite action's run: block directly interpolates `${{ github.action_path }}` inside the shell command string. Any ${{ ... }} expression inside a run: block is a script-injection risk because the value is substituted by the YAML template engine before the shell ever sees it, bypassing shell quoting. Offending line: `${{ github.action_path }}/entrypoint.sh`

Locations:

- `action.yml:35`

### script-injection (severity: high)

Sub-rule (a): The 'print-output' step in lint.yml directly interpolates `${{ steps.lint-with-config.outputs.logfile }}` and `${{ steps.lint-all-continue.outputs.logfile }}` inside run: shell commands (echo and cat). steps.*.outputs.* values are untrusted-input sources and must not be interpolated directly into run: blocks. Offending lines: `echo ${{ steps.lint-with-config.outputs.logfile }}`, `cat ${{ steps.lint-with-config.outputs.logfile }}`, `echo ${{ steps.lint-all-continue.outputs.logfile }}`, `cat ${{ steps.lint-all-continue.outputs.logfile }}`

Locations:

- `.github/workflows/lint.yml:52`

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable tags instead of full 40-character SHA commit hashes, making them vulnerable to supply-chain attacks if the tag is moved. Failing references: dockerimage.yml: `actions/checkout@v1`; lint.yml: `actions/checkout@v2`; release.yml: `actions/checkout@v2`, `haya14busa/action-update-semver@v1`.

Locations:

- `.github/workflows/dockerimage.yml:14`
- `.github/workflows/lint.yml:6`
- `.github/workflows/release.yml:11`
- `.github/workflows/release.yml:12`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no job within them defines job-level permissions either. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/dockerimage.yml:1`
- `.github/workflows/lint.yml:1`
- `.github/workflows/release.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all four findings:
1. action.yml script-injection: Moved `${{ github.action_path }}` to env block as ACTION_PATH, referenced as "$ACTION_PATH/entrypoint.sh" in run block.
2. lint.yml script-injection: Moved step output expressions to env block (LOGFILE_WITH_CONFIG, LOGFILE_ALL_CONTINUE), referenced as shell variables in run block.
3. Pinned all unpinned actions: actions/checkout@v1 → SHA 50fbc622, actions/checkout@v2 → SHA 0717577d, haya14busa/action-update-semver@v1 → SHA 7d2c5586.
4. Added `permissions: {}` top-level to all three workflow files (dockerimage.yml, lint.yml, release.yml). For release.yml, added job-level `permissions: contents: write` since the update-semver action needs to push tags.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed two script-injection findings: (1) In .github/workflows/dockerimage.yml, moved secrets.DOCKERHUB_USER and secrets.DOCKERHUB_TOKEN from inline ${{ }} interpolation in the run: block into a step-level env: block, referencing them as properly double-quoted $DOCKERHUB_USER and $DOCKERHUB_TOKEN in the shell script. (2) In entrypoint.sh, quoted the unquoted ${INPUT_FILE_OR_DIR:-.} expansion to "${INPUT_FILE_OR_DIR:-.}" to prevent shell metacharacter injection from the caller-controlled input, and removed the shellcheck disable comment that was suppressing the warning.

