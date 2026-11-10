<!-- markdownlint-disable -->

# Hardening Report: nolabs-ai--runseal/v0.3.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nolabs-ai--runseal/v0.3.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a), direct expression interpolation: the 'Setup Runseal' step's run: block puts a GitHub Actions expression straight into the shell command: `run: bash "${{ github.action_path }}/setup.sh"`. The step already sets RUNSEAL_ACTION_PATH: ${{ github.action_path }} in its env, so the line should use `bash "$RUNSEAL_ACTION_PATH/setup.sh"` or `bash "$GITHUB_ACTION_PATH/setup.sh"` instead.

Locations:

- `action.yml:50`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Changed the 'Setup Runseal' step in action.yml from bash "${{ github.action_path }}/setup.sh" to bash "$RUNSEAL_ACTION_PATH/setup.sh". The step's env already sets RUNSEAL_ACTION_PATH, and the shell is bash.

