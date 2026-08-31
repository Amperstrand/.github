# Provenance and License Practice for Amperstrand Shared Code

**Purpose:** This document encodes the fail-closed provenance audit discipline from the dissolved `amp-embedded-common` repo into an org-level engineering practice. Before extracting any code to a shared location (`.github` workflows, org actions, or reusable crates), a per-component provenance audit is mandatory.

## The Fail-Closed Rule

Before sharing code across repos, answer the following for each component:

1. **Expression, not ideas:** Is the content original expression or a standard ecosystem idiom?
   - Standard patterns (e.g., DWT cycle counting, HAL register sequences) are *ideas*, not protectable expression.
   - If the code is a restatement of a well-known idiom, it should not be extracted as "shared" — each project should own its inline copy or use the upstream API directly.

2. **Sole-author pre-flight:** If dual-licensing is contemplated (e.g., GPL → MIT+Apache), verify sole authorship first.
   - Only the original author can grant additional licenses.
   - Multi-author patches require a CLA or explicit relicensing agreement from every contributor.
   - This gate exists to prevent the "unblocking landing" anti-pattern: converting to permissive license without verifying authorship is not unblocking — it's license infringement.

3. **Owner-decision rule:** The final decision on whether to extract, keep, or dissolve shared code belongs to the repo owner.
   - Technical equivalence (e.g., "a 3-line function") is insufficient justification if the sharing cost exceeds benefit.
   - Consider: cross-repo rev-pinned dep overhead, maintenance burden, and consumer count.
   - If a component has exactly one consumer and no clear path to a second, it is not shared — it is private.

## Worked Example: The gm65-scanner Case

The `amp-embedded-common` repo's LICENSES.md investigation (lines 34-49) concluded that the `amp-dwt-watchdog` crate was original expression from the `ccid-firmware-rs` project and therefore GPL-2.0-or-later by repository provenance. This conclusion was correct.

However, the gate *proved the sharing thesis dead on arrival*: a GPL crate can never be consumed by MIT-licensed firmware like `gm65-scanner`. The crate's founding goal — "stop copy-paste between firmwares" — was structurally unreachable because license compliance correctly blocked the second consumer.

**Lesson extracted:** The provenance audit is not a compliance box-checking exercise; it is a feasibility test for the sharing thesis. If the gate says "no", the answer is not "find a workaround" — it is "do not share this code."

## When to Extract: The rust-embedded.yml Workflow Counterexample

The reusable CI workflow survived dissolution because it meets all three criteria:

1. **Expression, not ideas:** The workflow is original composition of standard building blocks (`dtolnay/rust-toolchain`, `Swatinem/rust-cache`) with Amperstrand-specific hardening (actionlint fixes, target-gated clippy, apt-in-lint-job). This is protectable expression.

2. **Sole-author pre-flight:** The workflow was authored in a single-ownership context within the org; no CLA is required for org-level reuse.

3. **Owner-decision rule:** The workflow has demonstrated cross-project value (consumed by `bolty-rs`) and follows the established industry pattern (dtolnay/.github, RustCrypto/actions). The owner-approved action is relocation to `Amperstrand/.github`, not dissolution.

## Application Checklist

Before extracting code to a shared location:

- [ ] Identify the component's origin (original expression vs. ecosystem idiom).
- [ ] Verify sole-authorship or relicensing clearance if dual-licensing is contemplated.
- [ ] Count actual consumers (not hypothetical ones). If zero or one, reconsider extraction.
- [ ] Confirm that extraction is the *simplest* solution (vs. inline copy, vendoring, or upstream PR).
- [ ] Document the provenance decision in the component's LICENSES.md or this practice doc.

## References

- Original audit: `.omo/evidence/amp-necessity-audit.md` (verdict table lines 287-296)
- Dissolved repo: `Amperstrand/amp-embedded-common` (archived post-Phase B)
- Industry patterns: `dtolnay/.github`, `RustCrypto/actions`
