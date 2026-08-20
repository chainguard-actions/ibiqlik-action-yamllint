<!-- markdownlint-disable -->

# Hardening Report: ibiqlik--action-yamllint/v3.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ibiqlik--action-yamllint/v3.1.1** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates `${{ github.action_path }}` into a shell command string. Any `${{ ... }}` expression inside a `run:` block is a script-injection risk because the value is substituted by the Actions template engine before the shell ever sees it, bypassing shell quoting. The offending line is: `${{ github.action_path }}/entrypoint.sh`. This should be replaced with the environment variable `$GITHUB_ACTION_PATH` which is already set by the runner.

Locations:

- `action.yml:36`

### script-injection (severity: high)

Sub-rule (b): In entrypoint.sh, the variable `${INPUT_FILE_OR_DIR:-.}` (sourced from `inputs.file_or_dir`, a workflow-controllable value) is expanded **unquoted** in the shell command: `yamllint "${options[@]}" ${INPUT_FILE_OR_DIR:-.} | tee -a "$LOGFILE"`. An unquoted shell expansion allows an attacker-supplied value containing shell metacharacters (`;`, `|`, `&`, `$(...)`, glob chars, whitespace) to be interpreted by the shell, enabling command injection. The file even suppresses the shellcheck warning for this with `# shellcheck disable=SC2086`. The fix is to quote the expansion: `"${INPUT_FILE_OR_DIR:-.}"`.

Locations:

- `entrypoint.sh:32`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed two script injection issues: (1) In action.yml, replaced `${{ github.action_path }}/entrypoint.sh` with `"$GITHUB_ACTION_PATH/entrypoint.sh"` — the runner already sets GITHUB_ACTION_PATH as an environment variable, so using it avoids the template-engine substitution that bypasses shell quoting. (2) In entrypoint.sh, quoted the unquoted expansion `${INPUT_FILE_OR_DIR:-.}` to `"${INPUT_FILE_OR_DIR:-.}"` to prevent shell metacharacter injection from attacker-controlled input values, and removed the `# shellcheck disable=SC2086` comment that was suppressing the warning about this vulnerability.

### Iteration 2

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings across three workflow files:

1. unpinned-uses: Pinned all four action references to full 40-char SHAs with tag comments preserved:
   - dockerimage.yml: actions/checkout@v1 → @50fbc622fc4ef5163becd7fab6573eac35f8462e # v1
   - lint.yml: actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26 # v3
   - release.yml: actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26 # v3
   - release.yml: haya14busa/action-update-semver@v1 → @7d2c558640ea49e798d46539536190aff8c18715 # v1

2. missing-permissions: Added top-level `permissions: {}` to all three workflow files. release.yml also gets a job-level `permissions: contents: write` since the update-semver action needs to push tag updates.

3. script-injection: Moved all ${{ }} expressions out of run: shell strings into step env: blocks:
   - dockerimage.yml: DOCKERHUB_USER and DOCKERHUB_TOKEN secrets moved to env: block, referenced as "$DOCKERHUB_USER" and "$DOCKERHUB_TOKEN"
   - lint.yml: Both steps.*.outputs.logfile values moved to env: block as LOGFILE_WITH_CONFIG and LOGFILE_ALL_CONTINUE, referenced with double-quotes in shell

