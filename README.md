# Mindful Moments

## Runbook: roll back the live site from v2 to v1

Use this procedure if v2 must be removed from production. It creates normal
revert commits, so the rollback remains auditable and does not rewrite history.
Do **not** use `reset`, a force-push, or delete either repository.

### Known versions

- Private source repository: `https://github.com/AhmedAttia906/mindful-moments`
- Source v2 commit: `43013f7a048dd0d83ee89d2b726598cade2e9a61`
- Original source v1 commit: `b9b5a492c30e1b9112ba56c9929db7b8ff790387`
- Public deployment repository: `https://github.com/AhmedAttia906/mindful-moments-public`
- Public v1 commit: `90d6009d7896a20c1a748d1998450c80c1ffde5b`
- Public v2 commit: `5e7df526a55fd0d51e0b18b15babb90dce60e0e9`
- Production URL: `https://ahmedattia906.github.io/mindful-moments-public/`

### 1. Revert v2 in the private source repository

Open PowerShell in the local `web` repository and run:

```powershell
git status --short
git switch main
git pull --ff-only origin main
git rev-parse HEAD
```

Stop if `git status --short` prints anything, or if `git rev-parse HEAD` does
not print `43013f7a048dd0d83ee89d2b726598cade2e9a61`. Resolve that discrepancy before
continuing.

Create and push a new commit that reverses only v2:

```powershell
git revert --no-edit 43013f7a048dd0d83ee89d2b726598cade2e9a61
git push origin main
```

Confirm that the local page contains the v1 word and no longer contains the v2
word:

```powershell
Select-String -LiteralPath index.html -Pattern 'today feel lighter'
Select-String -LiteralPath index.html -Pattern 'today feel gentler'
```

The first command must return the edited sentence. The second must return no
match.

### 2. Revert v2 in the public deployment repository

Use a new, empty temporary directory so the deployment repository cannot be
confused with the private source repository:

```powershell
$rollbackDir = Join-Path $env:TEMP 'mindful-moments-pages-rollback'
if (Test-Path -LiteralPath $rollbackDir) {
    throw "Rollback directory already exists: $rollbackDir"
}
git clone https://github.com/AhmedAttia906/mindful-moments-public.git $rollbackDir
git -C $rollbackDir switch main
git -C $rollbackDir pull --ff-only origin main
git -C $rollbackDir rev-parse HEAD
```

Stop unless the final command prints
`5e7df526a55fd0d51e0b18b15babb90dce60e0e9`.

Create the public rollback commit and push it:

```powershell
git -C $rollbackDir revert --no-edit 5e7df526a55fd0d51e0b18b15babb90dce60e0e9
git -C $rollbackDir push origin main
```

That push triggers GitHub Pages automatically. The production URL does not
change.

### 3. Verify production

Wait for the Pages deployment shown at
`https://github.com/AhmedAttia906/mindful-moments-public/actions` to complete,
then make an uncached request:

```powershell
$url = 'https://ahmedattia906.github.io/mindful-moments-public/?rollback=v1'
$response = Invoke-WebRequest -Uri $url -UseBasicParsing
$response.StatusCode
$response.Content -match 'today feel lighter'
$response.Content -match 'today feel gentler'
```

The expected results are:

```text
200
True
False
```

Finally, open the production URL on a device or network that is not signed in
to GitHub. The middle sentence must read:

> Small steps like resting, talking, or asking for help can make today feel lighter.

### Recovery if only one push succeeds

- If the private-source push succeeds but the public-deployment push fails,
  production remains on v2. Fix the deployment-repository problem and repeat
  step 2; do not revert the source rollback.
- If the public-deployment push succeeds but the private-source push fails,
  v1 is live. Fix the private repository and complete step 1 so source and
  production agree.
