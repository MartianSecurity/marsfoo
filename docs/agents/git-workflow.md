# Git Workflow

## Critical Rules

1. **NEVER** commit directly to main
2. **NEVER** merge branches into main directly
3. **NEVER** push to main - EVER
4. **NEVER** use `--force` without explicit approval
5. **ALWAYS** create new commits - never amend or rebase unless explicitly asked

**Forbidden commands:**

- `git push origin main`
- `git merge feature-branch` while on main
- Any direct commits to main

All changes to main MUST go through pull requests.

## Commit Policy

**"Explicitly asked"** = user says "amend", "squash", "rebase", or "fix up the commit".

"Looks good" or "go ahead" is NOT permission to rewrite history.

## Standard Workflow

### 1. Inspect and choose the branch

```bash
git status --short --branch
git worktree list
```

Continue an existing task branch for follow-up work. For independent work that needs current main, fetch it without changing the existing checkout, then create a new branch in an unused sibling worktree:

```bash
git fetch origin main
git worktree add -b feature/your-branch-name ../marsfoo-your-branch-name origin/main
```

Use the explicitly requested base when it differs. Never switch, reset, clean or pull an occupied checkout to follow this example; preserve tracked, untracked and ignored work.

### 2. Make changes; commit when authorized

```bash
git add <specific-files>    # NEVER blindly add everything
git commit -m "type(scope): description"
```

Commit/push only within the user's authorization; prior authorization in the session remains valid. See [PR conventions](pr-conventions.md) for commit message format.

### 3. Lint and Format

```bash
npm run l    # Lint changed files
npm run f    # Format changed files
```

These scripts write to files selected from the entire diff against `origin/main`. Inspect that set first; use scoped read-only checks for review or when it includes unrelated work. Fix task-related errors before proceeding.

### 4. Sync with Main

```bash
git fetch origin main
git merge origin/main
```

Sync only when needed for the authorized implementation/PR and preserve the reviewed head while reviewing. Resolve task-branch conflicts before pushing; do not rewrite history without authorization.

### 5. Push and Create PR

```bash
git push -u origin feature/your-branch-name
gh pr create --title "type(scope): description" --body "PR description"
```

### 6. Review and authorized merge

Wait for the required checks and review. Merge only within the user's authorization and against the reviewed head; do not bypass gates or rewrite shared history. Local implementation, commit/push, PR creation, merge and deployment are distinct scopes. Reuse authorization already given, but a generic request to improve code is not an explicit request to commit it.

After an authorized merge, follow the exact merge commit's `main` CI run to completion. Inspect failing-job logs before reacting. A passing test summary followed by a crashed worker can still indicate a resource leak; nondeterminism or a small diff alone does not prove an unrelated flake.

For a supported transient infrastructure failure, rerun only failed jobs and inspect the new attempt. Do not retry indefinitely or treat a billing/approval block as a transient test failure. For a confirmed change-caused regression, prepare a focused fix or revert PR; publishing/merging it must remain within the authorized scope. A recurring flake needs a cause and visible follow-up, not weaker assertions or broader timeouts.

Report the exact run/commit and its result, or identify the remaining failure and evidence linking it to infrastructure/base-branch state. Do not describe pending or blocked CI as green.

## Key Points

- **Never blindly `git add .`** - there may be unrelated files
- Inspect the current base and head before publishing; sync the task branch when needed without changing an unrelated checkout
- **Don't edit CHANGELOG.md** - it's auto-generated
