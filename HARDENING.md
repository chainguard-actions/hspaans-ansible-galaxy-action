<!-- markdownlint-disable -->

# Hardening Report: hspaans--ansible-galaxy-action/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **hspaans--ansible-galaxy-action/v1.1.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a mutable Docker image tag instead of a SHA digest: `image: docker://ghcr.io/hspaans/ansible-galaxy-action:1.1.0`. This is vulnerable to supply-chain attacks if the tag is overwritten. Additionally, container-ci.yml uses `hspaans/.github/.github/workflows/container-ci.yml@master` (mutable branch ref). container-release.yml uses multiple unpinned action refs: `actions/checkout@v4`, `docker/setup-qemu-action@v3`, `docker/setup-buildx-action@v3`, `docker/login-action@v3`, `docker/build-push-action@v5` — all mutable version tags instead of full 40-character commit SHAs.

Locations:

- `action.yml:12`
- `.github/workflows/container-ci.yml:11`
- `.github/workflows/container-release.yml:20`
- `.github/workflows/container-release.yml:23`
- `.github/workflows/container-release.yml:26`
- `.github/workflows/container-release.yml:29`
- `.github/workflows/container-release.yml:47`

### permissions (severity: medium)

container-ci.yml has no top-level `permissions:` key and its single job also has no `permissions:` key, granting default (potentially broad) permissions to the GITHUB_TOKEN.

Locations:

- `.github/workflows/container-ci.yml:1`

### permissions (severity: medium)

container-release.yml has no top-level `permissions:` key and its single job (`container-release`) also has no `permissions:` key, granting default (potentially broad) permissions to the GITHUB_TOKEN.

Locations:

- `.github/workflows/container-release.yml:1`

### script-injection (severity: high)

Rule (a): `${{ github.ref }}` is directly interpolated inside a `run:` shell command string in the 'Container Reference Name' step. An attacker who can control the ref name (e.g. via a specially crafted tag or branch name) could inject arbitrary shell commands. Offending line: `NAME=$(echo "${{ github.ref }}" | sed -e 's,.*/\(.*\),\1,; s/^v//')`

Locations:

- `.github/workflows/container-release.yml:43`

### github-env-injection (severity: high)

The 'Container Reference Name' step writes `NAME` — a value derived from the untrusted `${{ github.ref }}` expression — to `$GITHUB_ENV` without the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). An attacker-controlled ref name containing newlines could inject arbitrary environment variables into subsequent steps. Offending line: `echo "ref_name=$NAME" >> $GITHUB_ENV`

Locations:

- `.github/workflows/container-release.yml:47`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, script-injection, github-env-injection

**Notes:**

Fixed all 5 findings across 3 files:

1. action.yml: Pinned Docker image `ghcr.io/hspaans/ansible-galaxy-action:1.1.0` with SHA digest `sha256:e70a64946de4e55f16ab35001338380a3b7706d7825941449040486890a9acac`, preserving the `docker://` scheme and `:1.1.0` tag inline.

2. container-ci.yml: (a) Pinned reusable workflow `hspaans/.github/.github/workflows/container-ci.yml@master` to full SHA `880d91388b14f0155475ac3d961e4aa6d0225df8`. (b) Added top-level `permissions: {}` since the job only calls a reusable workflow.

3. container-release.yml: (a) Pinned all 5 action refs to full SHAs: actions/checkout@v4→11d5960a, docker/setup-qemu-action@v3→c7c53464, docker/setup-buildx-action@v3→8d2750c6, docker/login-action@v3→c94ce9fb, docker/build-push-action@v5→ca052bb5. (b) Added top-level `permissions: { contents: read, packages: write }` — minimum needed for checkout and ghcr.io push. (c) Fixed script-injection: moved `${{ github.ref }}` into step `env:` block as `GITHUB_REF` and used `$GITHUB_REF` in the shell script. (d) Fixed github-env-injection: sanitized the derived `NAME` value with `printf '%s' "$NAME" | tr -d '\n\r'` before writing to `$GITHUB_ENV`, and quoted `"$GITHUB_ENV"` properly.

