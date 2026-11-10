<!-- markdownlint-disable -->

# Hardening Report: nolabs-ai--runseal/v0.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **nolabs-ai--runseal/v0.2.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: a GitHub Actions expression is placed straight into the run script of the 'Setup Runseal' step: `run: bash "${{ github.action_path }}/setup.sh"`. The value is pasted into the script before the shell parses it. Fix: use the runner-provided env var instead, e.g. `run: bash "$GITHUB_ACTION_PATH/setup.sh"`, or the already-defined `"$RUNSEAL_ACTION_PATH/setup.sh"`.

Locations:

- `action.yml:49`

### github-env-injection (severity: high)

setup.sh is run by the 'Setup Runseal' composite step. It writes `install_dir` to $GITHUB_PATH with `echo "${install_dir}" >> "${GITHUB_PATH}"`. `install_dir` is built from `version`. When the version is not 'latest', resolve_version/strip_v pass it through unchanged from the workflow-controlled inputs `runseal-version` / `nono-version` (env RUNSEAL_VERSION / NONO_VERSION). There is no `printf '%s' ... | tr -d '\n\r'` step before the write, so a newline in the input could add extra PATH entries. Fix: sanitize the value right before each write, e.g. `safe=$(printf '%s' "$install_dir" | tr -d '\n\r'); echo "$safe" >> "$GITHUB_PATH"`. Validating the version against a semver regex would also help.

Locations:

- `setup.sh:118`
- `setup.sh:135`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection

**Notes:**

action.yml: the 'Setup Runseal' step now runs `bash "$GITHUB_ACTION_PATH/setup.sh"` instead of pasting the `${{ github.action_path }}` expression into the script. setup.sh: before each write to $GITHUB_PATH (the already-installed path, the fresh download path, and the build-from-source path), install_dir is cleaned with `printf '%s' | tr -d '\n\r'`. Each cleanup is a plain assignment, so errexit still works. safe_dir is declared local in install_release_binary. No other behaviour changed. Nothing was run or tested.

