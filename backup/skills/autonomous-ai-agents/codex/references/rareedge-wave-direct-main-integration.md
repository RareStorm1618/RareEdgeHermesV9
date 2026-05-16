# RareEdge Wave Direct-to-main Integration Playbook

Use when a RareEdge wave was implemented by multiple isolated Codex/Hermes worktrees and the user explicitly asks to commit/push everything to `main`.

## Trigger

- Several wave agent worktrees contain scoped uncommitted changes.
- The primary/main RareEdge worktree has unrelated dirty state or massive historical churn.
- User explicitly requests direct push to `https://github.com/RareStorm1618/RareEdgeV9.git main`.

## Pattern

1. **Do not stage from the dirty primary worktree.** Treat it as unsafe if `git status --short` contains unrelated files.
2. In each scoped agent worktree, run final cheap checks and create one clear commit per workstream.
3. Create a fresh integration worktree from `origin/main`, e.g. `RareEdgeV9-waveNN-integrate` on `hermes/waveNN-integrate`.
4. Cherry-pick the scoped wave commits into the integration worktree.
5. Resolve conflicts by preserving scoped code/test/env/migration/WO/RRbuild intent; for shared log files like `RRbuild.md`, avoid blindly choosing one side if it would erase prior integration evidence. Append a compact combined entry when needed.
6. Run verification from the integration worktree:
   - `git status --short --branch`
   - `git diff --check HEAD~N..HEAD`
   - `git ls-files -u`
   - search touched/resolved files for conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`)
   - available static/migration checks
   - targeted tests if dependencies are available
   - if full-repo TypeScript still fails from baseline debt, capture the full exit code plus a touched-path diagnostic filter proving no diagnostics in the files changed by the integration
7. Push with a per-command GitHub auth header from Hermes `.env` rather than rewriting remotes or credentials.
8. After push, fetch `origin main` and compare local `HEAD`, `origin/main`, and `git ls-remote origin refs/heads/main`; report all full SHAs.

## Pitfalls

- Do not use `git add .` in the primary worktree when it has unrelated dirty state.
- The terminal wrapper may append benign cache-file errors after successful git commands; rely on the command exit code and the actual git output, not those wrapper cleanup messages.
- A failed multi-line `cd && ...` shell can leave later git commands running outside the repo if command grouping is wrong. Prefer a Python subprocess push with explicit `cwd=repo` for critical push/verify steps.
- When `origin/main` already contains part of a WO from another wave, cherry-pick conflicts are expected. Resolve to the combined current intent and rerun static checks.
- Shared status files (`RRbuild.md`, work-order docs) need explicit preservation checks after conflicts: confirm existing WOs keep their prior statuses, the newly integrated WO status is not silently promoted beyond evidence, and any addendum records post-conflict verification rather than pre-cherry-pick evidence only.
- For frontend pub/sub or similar cross-cutting conflicts, run the directly touched unit tests plus adjacent client/hook/context/component tests; do not rely only on the tests that existed in the picked commit.

## Minimal push/verify snippet

```bash
python3 - <<'PY'
import os, subprocess, base64, pathlib
repo='/mnt/c/Users/MP3-Backup/Documents/GitHub/RareEdgeV9-waveNN-integrate'
head=subprocess.check_output(['git','rev-parse','HEAD'], cwd=repo, text=True).strip()
text=pathlib.Path('/mnt/c/Users/MP3-Backup/AppData/Local/hermes/.env').read_text(encoding='utf-8', errors='ignore')
token=next(line.split('=',1)[1].strip().strip('"').strip("'") for line in text.splitlines() if line.startswith('GITHUB_TOKEN='))
env=os.environ.copy()
env['GIT_CONFIG_COUNT']='1'
env['GIT_CONFIG_KEY_0']='http.https://github.com/.extraheader'
env['GIT_CONFIG_VALUE_0']='AUTHORIZATION: Basic '+base64.b64encode(('x-access-token:'+token).encode()).decode()
subprocess.run(['git','push','origin','HEAD:main'], cwd=repo, env=env, check=True)
subprocess.run(['git','fetch','origin','main'], cwd=repo, check=True)
remote=subprocess.check_output(['git','rev-parse','origin/main'], cwd=repo, text=True).strip()
print('LOCAL=', head)
print('REMOTE=', remote)
if remote != head:
    raise SystemExit('remote mismatch')
PY
```
