# Hardening Report: ibiqlik--action-yamllint/v3.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **ibiqlik--action-yamllint/v3.1.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The `run:` block in action.yml directly interpolates `${{ github.action_path }}` inside the shell command string. Per the security check rules, all `github.*` expressions must be treated as attacker-controlled and must be assigned to an environment variable first, then referenced as `$ENV_VAR` in the run step. The value should be assigned via `env: ACTION_PATH: ${{ github.action_path }}` and then used as `$ACTION_PATH/entrypoint.sh`.

Locations:

- `action.yml:38`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in action.yml at line 38. Moved `${{ github.action_path }}` out of the `run:` block and into the `env:` block as `ACTION_PATH: ${{ github.action_path }}`. Updated the shell command from `${{ github.action_path }}/entrypoint.sh` to `$ACTION_PATH/entrypoint.sh` to safely reference the environment variable instead of directly interpolating the GitHub context expression.

