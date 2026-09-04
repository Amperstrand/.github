# Amperstrand/.github

Org-level reusable workflows and engineering practice documentation for Amperstrand projects.

## Purpose

This repo hosts shared CI infrastructure and discipline docs that apply across the organization, decoupled from any specific product or license domain.

- **Reusable workflows** (`.github/workflows/`) — CI jobs that multiple repos consume via `uses:`.
- **Practice docs** (`LICENSES-PROVENANCE-PRACTICE.md`) — Engineering discipline for provenance and license decisions before extracting shared code.

## Workflows

### `gitleaks.yml` — org-wide secret scanning

Runs the pinned gitleaks binary (8.30.1) over the **full git history** of the caller repo.
No third-party action, no license dependency. Callers enroll with:

```yaml
on:
  push:
    branches: [main, master]
  pull_request:
  schedule:
    - cron: "23 4 * * *"

jobs:
  gitleaks:
    uses: Amperstrand/.github/.github/workflows/gitleaks.yml@main
```

A caller-root `.gitleaks.toml` is picked up automatically — use it for allowlists
(test fixtures with throwaway keys, remediated historical paths), each entry with a
comment saying **why** it is safe.

- **Reusable workflows** (`.github/workflows/`) — CI jobs that multiple repos consume via `uses:`.
- **Practice docs** (`LICENSES-PROVENANCE-PRACTICE.md`) — Engineering discipline for provenance and license decisions before extracting shared code.

## Usage

Repos consume workflows by reference:

```yaml
jobs:
  ci:
    uses: Amperstrand/.github/.github/workflows/rust-embedded.yml@main
    with:
      toolchain: "1.92"
      clippy-target: "thumbv7em-none-eabihf"
      apt-packages: "libpcsclite-dev"
      test-flags: "--workspace --exclude esp32-crate"
```

See each workflow's file for its full input specification.

## Design Rationale

This follows the dtolnay/.github pattern: org-meta repos for reusable infrastructure are the established industry convention. Benefits:

- **Discoverability:** Consumers can find all org workflows in one place.
- **Decoupling:** CI infrastructure lives separately from product crates, avoiding license entanglement.
- **Separation of concerns:** Practice docs live where they are applied — the org level — not hidden inside a dissolved repo's history.

## History

This repo was created in 2026-08 as part of the dissolution of `Amperstrand/amp-embedded-common`. The reusable `rust-embedded.yml` workflow was relocated here; all crate code was dissolved back into `ccid-firmware-rs`. See `.omo/evidence/amp-necessity-audit.md` for the full audit and verdict table.
