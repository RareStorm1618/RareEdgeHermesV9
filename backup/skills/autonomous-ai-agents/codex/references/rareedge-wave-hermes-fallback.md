# RareEdge Wave Fallback: Hermes Subagents + Worktree Audit

Use this when a RareEdge wave is planned for Codex CLI but standalone Codex cannot be used in the moment, or when parallel wave slices are better handled by Hermes `delegate_task` subagents.

## Pattern

1. Keep the existing class-level RareEdge prompt shape from `references/rareedge-workorder-codex.md`.
2. Create/assign one isolated worktree per agent line, named clearly enough to map back to the wave/agent, for example:
   - `RareEdgeV9-wave20-agent1`
   - `RareEdgeV9-wave20-agent2`
   - `RareEdgeV9-wave20-agent3`
3. Give each subagent a self-contained GOAL MODE prompt containing:
   - wave number and exact agent line
   - WO numbers and source WO paths if known
   - commit/push YES/NO
   - scoped-file rules
   - required checks and final response format
4. Treat subagent final summaries as untrusted self-reports. Parent Hermes must verify the filesystem state before reporting success.

## Post-subagent audit checklist

Run these per worktree before finalizing:

- `git status --short`
- `git diff --check`
- touched-file conflict-marker scan
- touched-file secret/credential-ish scan
- package-file noise check when JS dependencies are present
- targeted tests/typechecks for the touched routes/services/components where feasible
- inspect changed WO addenda and `RRbuild.md` for honest status and exact evidence

If a subagent times out or the parent loses its response, do not discard the worktree. Inspect the worktree directly: subagents may have left useful scoped changes even without a clean final message.

## Reporting rule

If changes remain only in separate wave worktrees and are not merged/staged/committed in the main repo, say so explicitly:

- list each changed worktree
- list the scoped changed files at a useful granularity
- report checks as PASS/FAIL/BLOCKED with exact commands and blockers
- separate repo-controlled local completion from external gates such as staging, Supabase, Redis, provider APIs, or deployment
- do not claim commit/push unless the parent verified the commit and remote state

## Rerunning previously blocked local checks

When a follow-up asks to rerun outstanding wave items, treat prior BLOCKED entries as stale until re-tested:

1. Re-discover the worktree/dependency state instead of trusting the previous blocker text.
2. Install or repair project dependencies only inside the isolated wave worktree/package needed for the focused check.
3. Rerun the exact targeted tests/typechecks that were previously blocked, plus `git diff --check` after any fix.
4. If a test failure is due to the generated code/test itself, fix the smallest scoped issue and rerun the focused check. Example: Vitest `vi.mock` factories are hoisted; any local variables referenced by the mock factory must be created with `vi.hoisted(() => ...)` or the suite can fail before collecting tests with `Cannot access '<mock>' before initialization`.
5. Update the wave `RRbuild.md` and WO verification addenda to replace stale dependency-blocked language with the new PASS/FAIL evidence and remaining non-local gates. Do not leave old blocker text after the rerun succeeds.

## What not to persist as a lesson

Do not encode transient setup failures as permanent constraints. If Codex auth, Rollup optional dependencies, TypeScript binaries, or other local dependencies are missing, record the exact blocker in the wave handoff but do not teach future agents that the tool or repo is generally unusable.