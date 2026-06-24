<!-- markdownlint-disable -->

# Hardening Report: hspaans--ansible-galaxy-action/v2.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **hspaans--ansible-galaxy-action/v2.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (b): The `run:` block in the second step builds a shell command string by interpolating `$ACTION_API_KEY` (sourced from `inputs.api_key`) unquoted into `command_string` on line 28: `command_string="$command_string --api-key $ACTION_API_KEY ${GITHUB_REPOSITORY%/*} ${GITHUB_REPOSITORY#*/}"`. This string is then executed via `eval "$command_string"` on line 31. Because `$ACTION_API_KEY` is not quoted when embedded into `command_string`, an attacker-controlled `api_key` value containing shell metacharacters (`;`, `&&`, `|`, `$(...)`, backticks, etc.) will be interpreted by the shell, enabling arbitrary command execution on the runner.

Locations:

- `action.yml:28`
- `action.yml:31`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection vulnerability in action.yml by replacing the unsafe eval-based command building pattern with a bash array approach. The original code embedded $ACTION_API_KEY unquoted into a command string and used `eval` to execute it, allowing shell metacharacters in the api_key input to be interpreted as shell commands. The fix uses a bash array (args) to build the command, properly double-quoting "$ACTION_API_KEY" when appending it to the array, and executes via "${args[@]}" instead of eval. This ensures the API key value is always treated as a single argument regardless of its content.

