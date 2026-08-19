<!-- markdownlint-disable -->

# Hardening Report: hspaans--ansible-galaxy-action/v1.0.5

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **hspaans--ansible-galaxy-action/v1.0.5** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple unpinned action/image references found using mutable tags instead of full 40-character SHA commit digests:

- action.yml: `runs.image: docker://ghcr.io/hspaans/ansible-galaxy-action:1.0.5` uses a mutable tag (`1.0.5`) instead of a SHA digest.
- container-release.yml: `uses: actions/checkout@v3`, `uses: docker/setup-qemu-action@v2`, `uses: docker/setup-buildx-action@v2`, `uses: docker/login-action@v2`, `uses: docker/build-push-action@v3` — all use version tags, not commit SHAs.
- container-ci.yml: `uses: hspaans/.github/.github/workflows/container-ci.yml@master` uses a mutable branch ref.

Locations:

- `action.yml:12`
- `.github/workflows/container-release.yml:21`
- `.github/workflows/container-release.yml:24`
- `.github/workflows/container-release.yml:27`
- `.github/workflows/container-release.yml:31`
- `.github/workflows/container-release.yml:52`
- `.github/workflows/container-ci.yml:10`

### missing-permissions (severity: medium)

Neither workflow file defines a top-level `permissions:` block, and neither job within them defines a job-level `permissions:` block. This means the GITHUB_TOKEN is granted its default (broad) permissions for the entire workflow run.

Locations:

- `.github/workflows/container-release.yml:1`
- `.github/workflows/container-ci.yml:1`

### script-injection (severity: high)

Sub-rule (a): A `${{ ... }}` expression is interpolated directly inside a `run:` shell command string. In the 'Container Reference Name' step, `${{ github.ref }}` is embedded directly in the shell command: `NAME=$(echo "${{ github.ref }}" | sed -e ...)`. An attacker who can control the ref value (e.g. via a crafted branch name) could inject arbitrary shell commands.

Locations:

- `.github/workflows/container-release.yml:40`

### github-env-injection (severity: high)

The 'Container Reference Name' step writes `$NAME` to `$GITHUB_ENV` without sanitization: `echo "ref_name=$NAME" >> $GITHUB_ENV`. The variable `$NAME` is derived from `${{ github.ref }}` (an attacker-controlled value) via sed substitution. No `printf '%s' ... | tr -d '\n\r'` sanitization is applied before the write, allowing newline injection that could set arbitrary environment variables for subsequent steps.

Locations:

- `.github/workflows/container-release.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection, github-env-injection

**Notes:**

Fixed all four findings:
1. unpinned-uses: Pinned container image in action.yml to SHA digest (preserving docker:// scheme and tag inline); pinned all 5 action references in container-release.yml to full commit SHAs with tag comments; pinned reusable workflow in container-ci.yml to full commit SHA.
2. missing-permissions: Added `permissions: {}` to container-ci.yml; added `permissions: contents: read / packages: write` to container-release.yml (minimum needed for checkout + ghcr.io push).
3. script-injection: Moved `${{ github.ref }}` from the run: shell string into the step's env: block as GITHUB_REF, referenced as $GITHUB_REF in the shell script.
4. github-env-injection: Added `printf '%s' "$NAME" | tr -d '\n\r'` sanitization before writing to $GITHUB_ENV, and quoted $GITHUB_ENV reference.

