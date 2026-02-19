# Non-Technical Quick Start

This is the simplest flow for non-technical users.

## Big Picture
1. Install `pg` once on your machine.
2. Install Memory-bank once in each project.
3. Start and end session every day.

## Terminal Choice
- VS Code terminal is recommended.
- External PowerShell also works.
- VS Code CMD also works for daily commands.

No admin is needed for normal commands.
Admin may be needed only if IT policy blocks installs (`winget`).

## A) One-Time Machine Setup
Open PowerShell and run:

```powershell
$gh = (Get-Command gh -ErrorAction SilentlyContinue).Source
if (-not $gh) {
  winget install --id GitHub.cli -e
  Write-Host "Restart terminal, then run this block again."
  return
}
& $gh auth status
if ($LASTEXITCODE -ne 0) { & $gh auth login --web --git-protocol https --hostname github.com }

$repo = "figchamdemb/Pgg-uni-memory-bank"
$tmp = Join-Path $env:TEMP "pg-install.ps1"
& $gh api -H "Accept: application/vnd.github.raw" "/repos/$repo/contents/pg-install.ps1?ref=main" > $tmp
powershell -ExecutionPolicy Bypass -File $tmp
```

Verify:

```powershell
pg version
```

## B) One-Time Per Project
In project root:

CMD:

```bat
pg install backend --target "%CD%"
```

PowerShell:

```powershell
pg install backend --target (Get-Location).Path
```

Cross-shell safe:

```bat
pg install backend --target .
```

Use `frontend` or `mobile` when needed.

## C) Every Work Session
Before coding:

```powershell
pg start -Yes
```

Optional:

```powershell
pg status
```

When ending shift:

```powershell
pg end -Note "finished for today"
```

## D) Using AI Chats
- Run `pg start -Yes` first in terminal.
- Then use Copilot/Claude/Codex chat normally.
- Run `pg end ...` before final summary/commit.

## E) Common Mistakes
- Running `pg install` every day.
- Running PowerShell syntax in CMD:
  - `(Get-Location).Path` is PowerShell only.
  - CMD equivalent is `%CD%`.
- Not reopening terminal after initial install.

If errors happen, open `TROUBLESHOOTING.md`.
