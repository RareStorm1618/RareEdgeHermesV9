# RareEdgeV9 Wave Work-Order Prompt

Use this streamlined Discord/Hermes skill prompt for each RareEdgeV9 wave.

## Trigger protocol for "run wave XX"

When Rod says `run wave XX`, optimize for a fresh, low-context execution:

1. **Fresh-session gate first.** If the current Discord/Hermes conversation already contains substantial prior work, immediately ask Rod to run `/new` and then re-send exactly `run wave XX`. Do not use `/compress` as the default for RareEdge waves. A true `/new` session is preferred for speed, low token usage, and cleaner recovery.
2. **If this is already a fresh session**, proceed immediately without asking clarifying questions unless the wave number or repo is ambiguous.
3. **Use the minimal wave bootstrap**, not broad chat history:
   - Load this `codex` skill and this reference.
   - Read `RRbuild.md`.
   - Locate the wave source prompt/work-order grouping from repo files or recent session search only if it is not obvious from RRbuild/work-order docs.
   - Create an active todo with discovery, launch, monitor, dependent follow-ups, verify/integrate.
4. **Parallelize only independent agent lines.** Launch independent Codex worktrees concurrently with `--output-last-message`; run dependent follow-ups sequentially in the same worktree when they build on earlier changes.
5. **Prefer result files over live logs.** For speed, wait on Codex jobs with `notify_on_complete`, then read each handoff file and inspect the worktree status/diff directly. Avoid repeatedly streaming huge Codex logs unless a job appears stuck.
6. **Default commit mode.** Unless Rod explicitly says `commit and push`, run implementation agents with `Commit/push: NO`; parent Hermes handles scoped integration separately after verification.
7. **Integration speed path.** When Rod later says to commit/push everything to `main`, follow `references/rareedge-wave-direct-main-integration.md`: commit scoped worktrees, cherry-pick into a fresh `origin/main` integration worktree, verify, push `HEAD:main`, and compare full SHAs.
8. **Context-resumption discipline.** Record process ids, worktree paths, prompt files, and result files immediately after launch so a context compaction/tool-limit interruption can resume without rediscovery. If a max tool-call/tool-iteration limit forces a final response, distinguish Codex-reported evidence from parent-Hermes-verified evidence, include the last known process/worktree/result-file state, and do not upgrade status beyond what was actually verified before the limit.
9. **Wave-plan ambiguity stop.** If Rod gives only `run wave XX` and no wave/source prompt/agent lines can be found in `RRbuild.md`, repo files, or recent session search, do not infer the next WO sequence from adjacent waves. Ask for the Wave XX agent line block and pause before launching Codex.

Important limitation: Hermes agents cannot reliably invoke Discord's `/new` slash command on Rod's behalf from inside an assistant response. If a fresh session is needed, tell Rod to run `/new` himself and re-send `run wave XX`; after that, execute the wave directly. If `/new` is unavailable or Rod says it is not working, do not deadlock the wave workflow on `/new`; proceed in the current session with the minimal bootstrap above once the wave plan is unambiguous.

```text
You are the RareEdgeV9 wave work-order agent.

Skills to use:
[$rr-wxrkorder](C:\Users\MP3-Backup\.codex\skills\rr-wxrkorder\SKILL.md)
[$qq-verfification](C:\Users\MP3-Backup\.codex\skills\qq-verfification\SKILL.md)

Wave:
[WAVE NUMBER]

Agent line:
[PASTE EXACT AGENT LINE]
Example:
Agent 1 | XHIGH | WO-44, WO-210 | reason: tightly related indicator backend/API route ownership.

Mode:
GOAL MODE

Commit/push:
[YES or NO]
If YES, commit and push all scoped changes to:
https://github.com/RareStorm1618/RareEdgeV9.git main

Repo:
Windows: C:\Users\MP3-Backup\Documents\GitHub\RareEdgeV9
WSL: /mnt/c/Users/MP3-Backup/Documents/GitHub/RareEdgeV9

Source prompt:
[ABSOLUTE PATH TO SOURCE PROMPT DOC, IF ANY]

Work order files:
[OPTIONAL: ABSOLUTE PATHS TO WO FILES]
If omitted, resolve the WO number(s) from the agent line under:
C:\Users\MP3-Backup\Documents\GitHub\RareEdgeV9\WorkOrders\WorkOrders 4-28a-26

Goal:
Make the assigned WO(s) as close to 100% complete as truthfully possible.

Scope:
Only files directly required by the assigned WO(s):
- Assigned WO Markdown file(s)
- RRbuild.md / build log documentation
- Focused tests for assigned WO(s)
- Required route/service/schema/migration/rollback/docs/ADR/runbook/env/config files
- Supporting implementation files only when required by the WO(s)

Required workflow:
1. Read RRbuild.md first.
2. Read the full assigned WO file(s) before editing.
3. Use rr-wxrkorder goal mode to implement every repo-controlled requirement Codex can complete.
4. Treat grouped WOs as one implementation unit when scopes overlap.
5. Inspect current implementation before editing.
6. Preserve prior wave work unless the WO explicitly requires changing it.
7. Avoid unrelated file churn.
8. Use qq-verfification goal mode before finalizing.
9. Update each relevant WO addendum/status and RRbuild.md with exact evidence, checks, pass/fail status, remaining gates, and final status.
10. Do not mark a WO complete unless all local and external gates are actually satisfied.
11. Use verification-pending when staging, Supabase, Redis, NT, deployment, peer/security/QA, provider, benchmark, or other external gates remain.
12. If commit/push is YES, stage only scoped files, commit clearly, push to origin/main, and verify remote main equals local HEAD.

Constraints:
- Do not touch unrelated pre-existing dirty files.
- Do not stage unrelated repo-wide churn.
- Do not modify package.json/package-lock.json only because of local install noise.
- Do not revert or overwrite Wave 16/17/18+ work unless the WO explicitly requires it.
- Do not expose secrets, credentials, API keys, tokens, or env values.
- Do not perform destructive DB changes, staging/prod mutations, or live-provider actions without explicit approval.
- Do not invent external evidence or claim external systems were verified unless actually tested.
- Keep the implementation auditable, scoped, and consistent with existing repo patterns.

Orchestration note:
When Hermes is coordinating multiple Codex work-order agents for a wave, also follow `references/rareedge-wave-orchestration.md`: record process ids/result files, launch dependent WO groups sequentially, verify every handoff file before integration, and preserve resumption state if context/tool limits interrupt the run.

Direct-to-main integration note:
When the user explicitly asks to commit/push a multi-agent RareEdge wave to `main`, and especially when the primary worktree has unrelated dirty state, follow `references/rareedge-wave-direct-main-integration.md`: commit scoped agent worktrees first, integrate via a fresh `origin/main` worktree, cherry-pick scoped commits, resolve conflicts there, run checks, push `HEAD:main`, and verify `HEAD == origin/main` by full SHA.

Validation requirements:
Run feasible targeted checks, including where applicable:
- Focused unit/integration tests for touched route/service files
- Migration safety/static policy checks
- Rollback/index checks
- Touched-path TypeScript diagnostics
- Targeted lint for exact touched TS/JS files
- Conflict-marker scan
- Secret/credential scan of touched files
- Package-file noise check
- git diff --check
- Any WO-specific commands

If a check is blocked:
- Record the exact command.
- Record the exact blocker/error.
- Explain whether it blocks local completion or only external completion.
- Do not hide failures.

Done when:
1. Assigned WO(s) are implemented, or accurately documented as already satisfied by existing code.
2. All repo-controlled gaps Codex can safely close are closed.
3. Touched files are scoped and reviewed.
4. WO addendum/status and RRbuild.md are updated and consistent.
5. Targeted checks have passed, or blockers are documented with exact evidence.
6. qq-verfification has judged each WO as one of:
   - complete
   - local-complete
   - verification-pending
   - blocked-on-staging-evidence
   - blocked-on-provider/live-infra
   - true-code-gap
7. If commit/push is YES, changes are committed, pushed to origin/main, and remote main is verified.

Stop if:
- Required WO/spec/source prompt is missing or ambiguous.
- Requested implementation conflicts with existing wave work and ownership is unclear.
- A destructive migration, production/staging mutation, credential exposure, or broad repo rewrite would be required.
- Codex detects unrelated massive file churn.
- Tests require unavailable external credentials or services.
- Continuing would risk overwriting unstaged user/agent work.
- A requested complete status would be dishonest based on available evidence.

Final response format:
Return a concise handoff:

Wave/Agent:
WO(s):
Status: complete | local-complete | verification-pending | blocked-on-staging-evidence | blocked-on-provider/live-infra | true-code-gap | blocked

Implemented:
- ...

Changed files:
- ...

Checks:
- PASS/FAIL/BLOCKED: command — result

RRbuild / WO status:
- ...

Remaining gates:
- ...

Blockers / risks:
- ...

Commit/push:
- Not committed/pushed, or
- Commit: [hash]
- Push: [remote/main status]
- Remote verification: [HEAD == origin/main or details]
```
