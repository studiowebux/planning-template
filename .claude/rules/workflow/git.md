# Git Workflow

## Branching Strategy

All code changes go through **feature branches → MR → default branch**.

```
feature branch → MR (CI + review) → main/master
```

### Default branches vary by repo — check `.planning/BRANCHES.md` Project Rules table.

### Workflow

1. **Create feature branch** from the default branch (or integration branch if applicable)
2. **Commit and push** to the feature branch
3. **Create MR** via `glab mr create` (see `branches.md` for squash flags)
4. **Wait for CI** to pass: `glab ci status`
5. **Merge via glab**: `glab mr merge <number>` with appropriate flags
6. **Clean up local**: `git checkout <default> && git pull && git branch -d <branch>`

### Protected Branches

**All repos have protected default branches — never push directly to main/master.**
Always merge via MR. See `.planning/BRANCHES.md` for per-repo rules.

## Commit Timing

### When to Commit
- After completing a goal phase or milestone
- After significant planning/documentation cleanup
- Before starting a new feature (clean working state)
- When user explicitly requests

### When NOT to Commit
- Mid-implementation (incomplete code)
- Work-in-progress planning files (task_plan.md, progress.md, findings.md)
- Uncommitted changes from previous sessions without user approval

## Commit Message Format

```
<type>: <short description>

<body - what changed and why>

Co-Authored-By: Claude <noreply@anthropic.com>
```

### Types
- `feat:` New feature
- `fix:` Bug fix
- `refactor:` Code restructuring
- `docs:` Documentation only
- `chore:` Maintenance (deps, config)
- `test:` Test additions or fixes

## Workflow with Planning

### Starting a Goal
1. Ensure working directory is clean (commit or stash pending changes)
2. Create planning files (task_plan.md, progress.md, findings.md)
3. Do NOT commit planning files until goal complete

### During Implementation
- Commit logical chunks as you go
- Each commit should be buildable/testable
- Reference goal/milestone in commit messages

### Completing a Goal
1. Final implementation commit
2. Archive planning files to `.planning/history/`
3. Commit archive move with goal summary
4. Update MILESTONES.md if applicable
5. Commit tracker updates

## Safety Rules

- NEVER use `--force` on `main` without explicit user approval
- NEVER commit secrets (.env, tokens, credentials, API keys)
- NEVER amend commits without explicit user request
- Check `git status` before and after commits
