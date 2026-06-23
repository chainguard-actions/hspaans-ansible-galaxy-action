<!-- markdownlint-disable -->

# Hardening Report: hspaans--ansible-galaxy-action/v1.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **hspaans--ansible-galaxy-action/v1.0.5** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml references a Docker image using a mutable tag ('1.0.5') instead of an immutable SHA digest. This means the image could be silently replaced with a different (potentially malicious) version without any change to the action reference, enabling supply-chain attacks. The failing reference is: image: 'docker://ghcr.io/hspaans/ansible-galaxy-action:1.0.5'. It should be pinned to a SHA digest, e.g. 'docker://ghcr.io/hspaans/ansible-galaxy-action@sha256:<64-hex-char-digest> # 1.0.5'.

Locations:

- `action.yml:12`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned the Docker image reference in action.yml from 'docker://ghcr.io/hspaans/ansible-galaxy-action:1.0.5' to 'docker://ghcr.io/hspaans/ansible-galaxy-action@sha256:a1f54799c9fc053c83ce13855330b39a5242377b524e73ef1404813818224416' # 1.0.5. The original tag is preserved as a comment for readability.

