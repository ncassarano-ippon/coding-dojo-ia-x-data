---
name: contribute
description: Full contribution workflow for this project — branch from lille, implement, run quality gate, conventional commit, open PR to lille. Use when starting any feature, fix, or improvement.
disable-model-invocation: true
allowed-tools: Bash(git *) Bash(uv run just *) Bash(gh *)
argument-hint: "[feat|fix|refactor|docs|chore] brief description"
---

## Current state
```!
git branch --show-current
git status --short
```

## Contribution rules
- Base branch is always `lille` — never branch from `main`
- Branch format: `<type>/<short-slug>` (e.g. `feat/retry-on-timeout`)
- Commits follow Conventional Commits: `<type>(<scope>): <description>`
- All PRs target `lille`
- Never commit: `.env`, `warehouse/*.duckdb`, `datalake/`, `pea_cache/`
- Never use `--no-verify`

## Steps

**1. Verify base**
If not on `lille`, warn the user and ask for confirmation before continuing.
If the working tree is dirty, ask whether to stash or stop.

**2. Create branch**
Derive `<type>/<short-slug>` from `$ARGUMENTS`. Then:
```
git switch lille && git pull origin lille && git switch -c <branch-name>
```

**3. Implement**
Make the changes described in `$ARGUMENTS`. Project conventions:
- Python: 120-char lines, Google docstrings, mypy strict, use `log_handler` logger
- SQL: UPPERCASE keywords, lowercase identifiers, max 230 chars/line

**4. Quality gate**
```
uv run just quality-all
```
Fix all errors (ruff, mypy, sqlfluff). Re-run until clean. After 3 failed attempts, stop and report remaining errors.

**5. Commit**
Stage only relevant files (no `.env`, no generated files). Commit:
```
git commit -m "<type>(<scope>): <short description>"
```
If the commit-msg hook rejects, revise and retry.

**6. Open PR**
```
git push -u origin <branch-name>
gh pr create --base lille --title "<commit message>" --body "..."
```
Return the PR URL to the user.
