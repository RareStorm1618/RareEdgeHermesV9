# Windows Kanban dispatcher fallback: parent-executed completion

When a Kanban worker card is valid but the dispatcher-spawned worker crashes before doing any work because of a local console/profile startup issue, the durable lesson is the recovery pattern, not a permanent claim that Kanban or the profile is broken.

## Pattern

1. Inspect the card with `hermes kanban --board <board> show <task_id> --json` and confirm whether any run actually modified state.
2. Add a Kanban comment explaining that the dispatched worker crashed before work and that the parent/operator is using a direct execution fallback.
3. Run the requested implementation or verification directly with the appropriate specialist tool/CLI, preserving the task scope.
4. Record exact checks, blockers, commit SHA, and push/remote verification as Kanban comments.
5. Complete the original implementation card and any dependent verifier card only after the direct fallback has produced verifiable artifacts.
6. Phrase the result as a fallback around the launcher/profile startup issue; do not encode a broad rule that Kanban workers cannot run on Windows.

## Good completion language

- `Completed via direct Codex fallback after dispatcher startup crash; commit <sha> pushed and remote verified.`
- `External gates remain: <list>; card is verification-pending, not externally complete.`

## What not to persist

Do not save one-off path, PID, missing-cache, or console error strings as durable constraints unless they recur and a concrete setup fix is known. Capture the operator workflow and verification evidence instead.
