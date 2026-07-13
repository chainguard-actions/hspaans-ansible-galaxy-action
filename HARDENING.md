<!-- markdownlint-disable -->

# Hardening Report: hspaans--ansible-galaxy-action/v2.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **hspaans--ansible-galaxy-action/v2.0.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (b) violation: In the 'Install dependencies' step of action.yml, the env var ACTION_API_KEY (sourced from inputs.api_key) is interpolated unquoted into the command_string variable: `command_string="$command_string --api-key $ACTION_API_KEY ${GITHUB_REPOSITORY%/*} ${GITHUB_REPOSITORY#*/}"`. This string is then passed to `eval "$command_string"`, meaning any shell metacharacters in the api_key input (e.g. semicolons, backticks, $(...)) will be interpreted by the shell, enabling command injection.

Locations:

- `action.yml:24`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in the 'Install dependencies' step of action.yml. Replaced the unsafe pattern of building a command string with unquoted `$ACTION_API_KEY` and executing it via `eval "$command_string"` with a bash array approach. The API key is now stored as a properly double-quoted array element (`"$ACTION_API_KEY"`), and the command is executed via `"${args[@]}"` which preserves argument boundaries and prevents shell metacharacters in the API key from being interpreted as shell commands. The `eval` call is completely eliminated.

