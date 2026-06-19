# Hardening Report: ibiqlik--action-yamllint/v3.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **ibiqlik--action-yamllint/v3.0.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The `run:` block in action.yml directly interpolates the GitHub Actions expression `${{ github.action_path }}` into the shell command string (`run: ${{ github.action_path }}/entrypoint.sh`). Per security best practice, `github.*` context values should be assigned to an environment variable first and then referenced as `$ENV_VAR` in the shell command, rather than being interpolated directly into the `run:` string. All `inputs.*` values are correctly passed via `env:` variables.

Locations:

- `action.yml:28`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in action.yml at line 28. Moved `${{ github.action_path }}` out of the `run:` shell string and into the step's `env:` block as `ACTION_PATH: ${{ github.action_path }}`. The run command was updated from `run: ${{ github.action_path }}/entrypoint.sh` to `run: "$ACTION_PATH/entrypoint.sh"`, referencing the value as a plain shell environment variable.

