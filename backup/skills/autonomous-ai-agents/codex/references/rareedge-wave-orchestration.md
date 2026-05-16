# RareEdge wave orchestration notes

Use this when Hermes is orchestrating multiple Codex work-order agents for a RareEdge wave.

## Fast trigger path

When Rod says `run wave XX`, first prefer a fresh Hermes session. If the conversation is not fresh, tell him to run `/new` and re-send `run wave XX`; do not default to `/compress` for wave launches. Once in a fresh session, execute immediately with a minimal bootstrap: load `codex`, read `RRbuild.md`, resolve the wave source/agent lines, create todos, launch worktrees, and record process/result handles.

## Durable practices

- Launch only independent WO groups in parallel. If the wave plan says one WO depends on another, run the dependency first, wait for its handoff, then launch the follow-up in the same scoped worktree when it must build on prior changes.
- Prefer at most 3 concurrent Codex agents per batch unless the machine is clearly idle; start dependency follow-ups as soon as their prerequisite handoff is verified instead of waiting for unrelated long jobs.
- For every background Codex launch, immediately record the worktree path, branch, process/session id, prompt file, and `--output-last-message` path in the active todo/status. This makes resumption after context compression or tool limits possible.
- Use explicit branch/worktree names with numeric suffixes for each isolated line, e.g. `hermes/wave21-agent1`, `hermes/wave21-agent5`. Avoid shell constructs that can be eaten by the terminal wrapper; if looping, verify the expanded branch/path before running `git worktree add`.
- Before final integration, gather each handoff file and verify the worktree status directly. Treat a missing handoff file as "process incomplete or failed," not as success.
- Favor direct status/diff/check commands and handoff files over huge live log reads. Only inspect detailed Codex logs when a process is stuck, failed, or missing its result file.
- When a tool-call limit or context compression interrupts the run, preserve the current state in the final/status message: active process ids if known, result-file paths, dirty worktrees, completed handoffs, unverified follow-ups, and exact checks still blocked.

## Verification posture

- Do not promote a WO past `verification-pending` merely because static checks passed. If focused Vitest/ESLint/pytest or external gates were blocked by local dependency/network state, leave the status pending and document the exact blocked command/error in the WO addendum and handoff.
- When multiple Codex agents update `RRbuild.md`, expect integration conflicts or duplicate entries during merge. Review and reconcile build-log entries manually before commit/push.
