<!-- markdownlint-disable -->

# Hardening Report: hspaans--ansible-galaxy-action/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **hspaans--ansible-galaxy-action/v1.1.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image reference with a mutable tag instead of a SHA digest. `image: 'docker://ghcr.io/hspaans/ansible-galaxy-action:1.1.0'` uses the tag `1.1.0`, which can be silently replaced with different (potentially malicious) content. It should be pinned to a full SHA256 digest, e.g. `docker://ghcr.io/hspaans/ansible-galaxy-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:12`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker container image in action.yml from the mutable tag `ghcr.io/hspaans/ansible-galaxy-action:1.1.0` to the immutable digest `ghcr.io/hspaans/ansible-galaxy-action@sha256:e70a64946de4e55f16ab35001338380a3b7706d7825941449040486890a9acac` (with `# 1.1.0` comment outside the YAML quotes for readability).

