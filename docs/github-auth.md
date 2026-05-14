# GitHub Auth Notes

Last checked: 2026-05-14

## Hermes `.env`

`GITHUB_TOKEN` is set in:

```text
C:\Users\MP3-Backup\AppData\Local\hermes\.env
```

The token was verified without printing it:

- `GET /user` succeeds as `RareStorm1618`.
- Repo API access works.
- Temporary repo branch create/delete was verified previously.

The token is not stored in this repository.

## Windows

GitHub CLI is installed and authenticated as `RareStorm1618`.

SSH authentication to `git@github.com` succeeds using:

```text
C:\Users\MP3-Backup\.ssh\github_raredge
```

## WSL Ubuntu

GitHub CLI is installed through `apt` and authenticated as `RareStorm1618`.

`gh` HTTPS operations work:

```bash
gh api user --jq .login
gh repo view RareStorm1618/RareEdgeHermesV9
```

SSH authentication to `git@github.com` succeeds using:

```text
~/.ssh/github_raredge
```

GitHub's `ssh -T git@github.com` returns exit code `1` even on success because GitHub does not provide shell access. The success indicator is the message:

```text
Hi RareStorm1618! You've successfully authenticated, but GitHub does not provide shell access.
```

## Gist Scope

Hermes code work only needs repo read/write access. A gist-specific write failure means that particular token lacks gist permission; it does not block normal repo operations.
