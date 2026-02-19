# Pgg Universal Memory-bank - Public Install Guide

This public repository contains installation and usage instructions.
Core installer logic and templates are in a private repository with access control.

## Quick Links
- Non-technical guide: `NON_TECHNICAL_QUICKSTART.md`
- Troubleshooting guide: `TROUBLESHOOTING.md`

## Access Requirements
1. You must be added as collaborator/team member on private repo `figchamdemb/Pgg-uni-memory-bank`.
2. You must authenticate locally using that same GitHub account.

```powershell
gh auth login --web --git-protocol https --hostname github.com
```

If access is missing, install commands return `403` or `404`.

### Owner Setup (one-time)
1. Open private repo `Settings` -> `Collaborators and teams`.
2. Invite developer account.
3. Developer accepts invite.

## Which Terminal Should I Use?
- VS Code terminal is recommended for daily use.
- External PowerShell is also fine for initial setup.
- If VS Code opens CMD by default, that is also supported.

Permissions:
- Normal user is enough for `pg` commands.
- Admin may be needed only if your machine blocks `winget` installs.

## Setup Order (Important)
1. One-time per machine: install `pg` global command.
2. One-time per repo: run `pg install backend|frontend|mobile`.
3. Every session: run `pg start -Yes` before coding, and `pg end` when done.

## Step 0: Ensure GitHub CLI
PowerShell:

```powershell
$gh = (Get-Command gh -ErrorAction SilentlyContinue).Source
if (-not $gh) {
  winget install --id GitHub.cli -e
  Write-Host "Restart terminal, then rerun."
  return
}
& $gh auth status
if ($LASTEXITCODE -ne 0) { & $gh auth login --web --git-protocol https --hostname github.com }
```

## Step 1: One-Time Global `pg` Setup
PowerShell:

```powershell
$gh = (Get-Command gh -ErrorAction SilentlyContinue).Source
if (-not $gh) { $gh = "C:\Program Files\GitHub CLI\gh.exe" }
$repo = "figchamdemb/Pgg-uni-memory-bank"
$tmp = Join-Path $env:TEMP "pg-install.ps1"
& $gh api -H "Accept: application/vnd.github.raw" "/repos/$repo/contents/pg-install.ps1?ref=main" > $tmp
powershell -ExecutionPolicy Bypass -File $tmp
```

Verify:

```powershell
pg version
```

## Step 2: Install Into a Project
Run in project root.

Backend:

```powershell
pg install backend
```

Frontend:

```powershell
pg install frontend
```

Mobile:

```powershell
pg install mobile
```

CMD explicit target path:

```bat
pg install backend --target "%CD%"
```

PowerShell explicit target path:

```powershell
pg install backend --target (Get-Location).Path
```

Cross-shell safe:

```bat
pg install backend --target .
```

## Step 3: Daily Session Commands
Start before coding:

```powershell
pg start -Yes
```

Status:

```powershell
pg status
```

End shift:

```powershell
pg end -Note "finished for today"
```

Notes:
- `pg install` is not daily; it is one-time per repo.
- `pg start` / `pg end` are daily session commands.

## VS Code + AI Chat Workflow
- Start in terminal with `pg start -Yes`.
- Continue work in Copilot/Claude/Codex chat.
- End in terminal with `pg end ...` before final summary/commit.

## What Gets Installed
- `Memory-bank/` structure and docs
- `AGENTS.md` start/end protocol
- local hook guard and CI workflow
- summary/generation scripts
- session bootstrap scripts

Default enforcement mode is `warn`.

Switch to strict later:

```powershell
git config memorybank.mode strict
```

## Security / IP Notes
- This repo is public for discoverability.
- Installer internals stay in private repo access control.
- Authorized users can inspect local outputs/scripts they run.
- Do not store secrets in Memory-bank docs or scripts.
