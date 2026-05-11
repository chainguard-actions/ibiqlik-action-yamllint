# Hardening Report: ibiqlik--action-yamllint/v3.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **ibiqlik--action-yamllint/v3.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

In action.yml, the `run:` block directly interpolates the GitHub Actions expression `${{ github.action_path }}` into the shell command string (used as `${{ github.action_path }}/entrypoint.sh`). This is a `github.*` context value embedded directly in a `run:` block rather than being assigned to an environment variable first and referenced as `$ENV_VAR`. The correct pattern would be to set `ACTION_PATH: ${{ github.action_path }}` in the `env:` block and then use `"$ACTION_PATH/entrypoint.sh"` in the run script.

Locations:

- `action.yml:36`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script-injection in action.yml at line 36. Moved `${{ github.action_path }}` out of the `run:` block into the `env:` block as `ACTION_PATH: ${{ github.action_path }}`, and updated the shell command from `${{ github.action_path }}/entrypoint.sh` to `"$ACTION_PATH/entrypoint.sh"`. This follows the correct pattern of assigning GitHub context expressions to environment variables and referencing them as plain shell variables.

