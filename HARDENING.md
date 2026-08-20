<!-- markdownlint-disable -->

# Hardening Report: ibiqlik--action-yamllint/v3.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ibiqlik--action-yamllint/v3.0.2** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates the GitHub Actions expression `${{ github.action_path }}` inside the shell command string. Any `${{ ... }}` expression interpolated directly in a `run:` block is a script-injection risk because the value is substituted by the Actions runner before the shell ever sees it, bypassing shell quoting. The offending line is: `run: ${{ github.action_path }}/entrypoint.sh`. This should be replaced with the safe environment variable equivalent, e.g., set `ACTION_PATH: ${{ github.action_path }}` in the `env:` block and use `"$ACTION_PATH"/entrypoint.sh` in the `run:` script.

Locations:

- `action.yml:30`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in action.yml line 30: moved `${{ github.action_path }}` from the `run:` shell command string into the `env:` block as `ACTION_PATH: ${{ github.action_path }}`. The `run:` command now uses `"$ACTION_PATH/entrypoint.sh"` instead of `${{ github.action_path }}/entrypoint.sh`, preventing the Actions runner from substituting the value directly into the shell command before the shell processes it.

### Iteration 2

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three workflow files: (1) Pinned all action references to full commit SHAs — actions/checkout@v1 → 50fbc622fc4ef5163becd7fab6573eac35f8462e, actions/checkout@v2 → 0717577d45739eb3c851188b29f50ed6c0b2194e, haya14busa/action-update-semver@v1 → 7d2c558640ea49e798d46539536190aff8c18715. (2) Added `permissions: {}` at the top level of all three workflow files; release.yml also gets job-level `permissions: contents: write` since the update-semver action needs to push tags. (3) Moved `${{ secrets.DOCKERHUB_USER }}` and `${{ secrets.DOCKERHUB_TOKEN }}` from the `run:` shell string into an `env:` block in dockerimage.yml, referencing them as `"$DOCKERHUB_USER"` and `"$DOCKERHUB_TOKEN"` in the shell script.

