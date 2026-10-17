# Local CI gate — reusable pattern

The verification-gate pattern from OpenTollGate/tollgate-module-basic-go
PR #955 (`scripts/pr-verify.sh`): check out a PR ref into a temp
worktree, run the repo's full local suite battery, post the results
table as a PR comment. Born because GitHub Actions is org-blocked for
tbmg; generalizes to any repo whose suites run on the bench.

## Per-repo adoption

1. Copy `pr-verify.sh` from tollgate-module-basic-go (scripts/).
2. Replace the suite list with this repo's battery (fmt / clippy /
   workspace tests / repo-specific gates).
3. Use from reviews: `scripts/pr-verify.sh <PR-number>` — the stamp
   lands on the PR as `### Local CI gate — PR #N`.

The stamp is the merge evidence the tbmg review rule consumes; in
Amberstrand repos it is the manager's pre-merge gate.
