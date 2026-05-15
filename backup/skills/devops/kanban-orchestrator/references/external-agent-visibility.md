# External Agent Visibility Pattern

Use this when implementation is performed outside Hermes Kanban by a long-running CLI agent (for example Codex CLI) but the user still wants the Hermes dashboard/board to show progress.

## Pattern

1. Create a Kanban card for the implementation lane before starting the external agent.
2. Put the repo/workspace path, work-order IDs, prompt file path, expected validation commands, and external process/session ID in the card body or comments.
3. If a separate verification pass is required, create a child verifier card with `parents=[implementation_card]` so the board shows the gate explicitly.
4. When the external agent completes, update/complete the implementation card with:
   - What changed.
   - What was intentionally left unchanged, including unrelated pre-existing dirty files that were deliberately preserved.
   - Exact validation commands and pass/fail status.
   - Commit/push status and, when pushed, the verified local SHA vs remote SHA/ref.
   - Remaining external gates or owner decisions.
5. Start the verifier only after the implementation card is complete, unless verification is truly independent.
6. Complete or block the verifier card based on evidence. If blocked, state the precise missing gate rather than marking the work order complete.
7. If the parent conversation is compacted or interrupted while the external process is running, resume from the process completion notification plus the recorded card comments. Poll/read the process result when tools are available; if the notification already contains the final verifier summary, use it as evidence but still update the Kanban card and repo state explicitly.
8. After verification, reconcile side effects: commit/push any intended documentation or status notes, then complete the verifier card with the commit IDs and remaining gates. A completed verifier card can still mean the underlying work order remains `verification-pending`.

## CLI creation notes for long external-agent cards

When the Kanban body is long Markdown (paths, instructions, handoff requirements), avoid inline heredocs or nested command substitution inside a quoted `hermes kanban create` command. They are brittle across bash-on-Windows, WSL, and `cmd` boundaries and can fail before creating the card.

Safer pattern:

1. Write the card body to a temp `.md` file.
2. Write a small temp shell script that reads `BODY=$(cat /path/body.md)` and invokes the Kanban CLI with `--body "$BODY"`.
3. Run the script, capture the returned task id, then create any dependent verifier card with `--parent <implementation_id>` in the original create call.
4. Verify with `kanban show <id> --json` that the implementation is `ready`/`running`, the verifier is `todo`, and the child lists the implementation as its parent.

On Windows-hosted Hermes installs where the WSL shell does not have `hermes` on `PATH`, use the repo-local Windows entrypoint from the Hermes checkout (for example `./venv/Scripts/hermes.exe kanban ...`) rather than inventing a new path. Prefer the board that already contains the project history instead of creating a near-duplicate board slug.

## Completion discipline

Do not mark the underlying feature/work order complete just because the implementation agent finished or local targeted tests passed. Keep it `verification-pending` or blocked when unresolved gates remain, such as:

- Ownership decision needed between overlapping work orders.
- Authenticated staging smoke tests not run.
- DB migration apply/rollback evidence requires controlled infrastructure.
- Peer/security/QA approval is still pending.
- Runtime topology smoke (worker/listener/deployment process) has not been demonstrated.

## Reporting

In the user-facing summary, separate these statuses:

- External agent finished.
- Kanban implementation card completed.
- Verifier card pending/completed/blocked.
- Work order complete vs. verification-pending.
- Commit/push status.

This prevents a visible dashboard card from implying the underlying work order is fully accepted.