# Hardening Report: ibiqlik--action-yamllint/v3.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `ff50f15e4b79bfbf764dafdfd2579175a6ea9771`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **ibiqlik--action-yamllint/v3.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

In action.yml, the expression `${{ github.action_path }}` is interpolated directly inside a `run:` shell command block rather than being assigned to an environment variable first. Although `github.action_path` is not directly attacker-controlled, it is a `github.*` context expression used inline in a shell command, violating the safe pattern of routing context values through `env:` variables. The run block executes: `${{ github.action_path }}/entrypoint.sh`.

Locations:

- `action.yml:36`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in action.yml at line 36: moved `${{ github.action_path }}` out of the `run:` shell block and into the `env:` block as `ACTION_PATH: ${{ github.action_path }}`. The shell command now uses `"$ACTION_PATH/entrypoint.sh"` instead of `${{ github.action_path }}/entrypoint.sh`, following the safe pattern of routing GitHub context expressions through environment variables.

