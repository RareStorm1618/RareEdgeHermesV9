# RareEdge Work Order Codex Dispatch Notes

Use this reference when the user provides a RareEdge wave/agent block such as:

```text
Wave 16
Agent 1 | XHIGH | WO-44, WO-210 | reason: tightly related indicator backend/API route ownership; one agent reconciles overlapping catalog/ranking/detail route scopes and Skill Library schema expectations.
```

## Source locations

Local project:

```text
C:\Users\MP3-Backup\Documents\GitHub\RareEdgeV9
/mnt/c/Users/MP3-Backup/Documents/GitHub/RareEdgeV9
```

Work order folder used in this session:

```text
C:\Users\MP3-Backup\Documents\GitHub\RareEdgeV9\WorkOrders\WorkOrders 4-28a-26
/mnt/c/Users/MP3-Backup/Documents/GitHub/RareEdgeV9/WorkOrders/WorkOrders 4-28a-26
```

## Parsing workflow

1. Parse each line as: `Agent <n> | <intelligence> | <WO list> | reason: <why grouped>`.
2. Locate each WO by tolerant filename matching, accepting both `WO-44` and `WO 44` forms.
3. Read all WO files in the group before dispatching implementation; include the grouping reason in the prompt.
4. Treat grouped WOs as one implementation unit when the reason says route/schema ownership overlaps.
5. Run Codex from the repo root. In WSL, if plain `codex` resolves incorrectly, use `/home/mp3/.local/bin/codex` until the parent bot process restarts.
6. Use `pty=true`; for long implementation jobs prefer background process monitoring.

## Example located files

For the example above in `WorkOrders 4-28a-26`:

- `Phase 3  WO 44 Build Indicator Library Backend.md`
- `Phase 3  WO 210 Build Indicator Library API Routes (catalog, rankings, detail).md`

## Prompt shape

Include:

- Wave and agent line verbatim.
- Intelligence level (e.g. `XHIGH`) as the expected reasoning depth.
- Absolute repo root.
- Absolute WO file paths and the full contents or concise extracted specs.
- Instruction to inspect current implementation before editing.
- Instruction to avoid unrelated changes.
- Required validation commands if known; otherwise ask Codex to discover and run targeted tests plus relevant type/lint checks.

## Windows-hosted execution and commit discipline

RareEdge runs often happen on a Windows-hosted Hermes session where the default terminal is bash/WSL-ish but the repo and authenticated GitHub tooling are Windows-native. Prefer the execution path that matches the repo/auth state rather than forcing everything through WSL.

- For Codex CLI jobs, Windows `codex.cmd` from `cmd.exe /c "cd /d C:\\Users\\MP3-Backup\\Documents\\GitHub\\RareEdgeV9 && ..."` can be the reliable path when WSL Codex auth fails or resolves to the wrong shim.
- For git commit/push in this repo, Windows git via `cmd.exe /c "cd /d ... && git ..."` may avoid WSL auth gaps and massive line-ending/noise views. Do not trust a WSL `git status` explosion as the authoritative worktree state if Windows git shows the expected scoped changes.
- In nested `cmd.exe /c` commands launched from bash, keep quoting simple. If commit-message quoting is brittle, use a hyphenated single-token message (for example `docs-wave16-verification-recheck`) or run a second simpler command rather than staging broad unintended changes.
- Stage only the intended work-order/RRbuild files when possible. If a prior failed staging attempt left exactly the intended files staged, verify with Windows `git status --short` before committing.
- Commit verification/documentation notes separately from adjacent wave notes when both are present, so Wave 16 rechecks and Wave 17/WO-605 rechecks remain auditable.

## Verification-pending discipline

For RareEdge work orders, Codex may finish the local implementation pass without making or preserving code changes if the repo already contains overlapping later-wave work. In that case, preserve the current implementation unless the work order explicitly requires reverting it, and document the ownership decision needed.

When summarizing or updating WO/RRbuild files, separate:

- Code changes made in this pass.
- Existing or concurrent changes intentionally preserved.
- Local checks that passed.
- External/non-local gates still required.
- Whether the WO is complete or only verification-pending.

Common verification-pending gates include controlled Supabase/Timescale migration apply/rollback evidence, authenticated staging route smoke, deployment-topology worker/listener smoke, peer/security/QA approval, and explicit ownership decisions between overlapping work orders.

## Focused frontend verification on Windows-mounted repos

RareEdge frontend checks may run from a Windows-hosted repo through a Linux/WSL-ish shell. If a focused Vitest invocation through `npx` or npm scripts resolves through the wrong optional native package set (for example Rollup/esbuild platform packages), do not immediately mark the implementation failed. Use a bounded verification pattern:

1. Confirm/install dependencies only as needed for the local check, but review `frontend/package.json` and `frontend/package-lock.json` afterward; do not stage package-file churn that is only dependency-install noise.
2. Prefer the project-local test binary for focused collection, e.g. `frontend/./node_modules/.bin/vitest run <targeted test files>`, so the command uses the installed project toolchain directly.
3. Rerun the touched-path TypeScript diagnostic filter, targeted lint, migration policy checks, and `git diff --check` after any fix.
4. If local focused tests pass but staging, migration apply/rollback, Redis/listener, Rust forwarding, or approval gates remain, commit with a clear `verification-pending` message and document the external gates in the WO addendum/RRbuild rather than claiming 100% completion.
