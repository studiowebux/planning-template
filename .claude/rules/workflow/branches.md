# Branch Management

## Branch Registry

**Read `.planning/BRANCHES.md` before any implementation phase.** It tracks active branches, base branches, worktree paths, and project rules across all repos.

## Rules

- **Check before coding**: Know which repo + branch + base you're working in
- **Plan branches per phase**: Each implementation phase in `task_plan.md` should note which branches it touches
- **Update on change**: Create, merge, or abandon a branch → update BRANCHES.md in the same session
- **Log in progress.md**: Note branch operations (checkout, push, merge) with repo + commit hash
- **Worktree column**: Populate when using git worktrees; leave empty when using standard checkout

## Merging — Always Use MR

**All repos have protected default branches. Never push directly to main/master.**

1. **Create feature branch** and push: `git push -u origin <branch>`
2. **Create MR** via `glab mr create` with `--squash-before-merge --remove-source-branch`
3. **Wait for CI**: `glab ci status` — pipeline must pass before merge
4. **Merge via glab**: `glab mr merge <number> --squash --remove-source-branch --yes`
5. **Clean up local**: `git checkout main && git pull && git branch -d <branch>`

### Per-Project Squash Flags

Check the Project Rules table in BRANCHES.md before merging:

| Squash setting | `glab mr create` flag | `glab mr merge` flag |
|---------------|----------------------|---------------------|
| off (optional) | `--squash-before-merge` | `--squash` |
| default on | (auto) | `--squash` |
| always (required) | (auto) | (auto) |

### Approval Requirements

Some repos require specific approvals (tracked in Project Rules table). If "Approvals" is not "none", ensure the reviewer has approved the MR before merging.

## Populating Project Rules

If the Project Rules table in BRANCHES.md has `?` entries or a new repo is added, query GitLab:

```bash
# From inside the repo's local clone:
cd <repo_path>

# Get merge settings
glab api projects/:id | python3 -c "
import sys,json; p=json.load(sys.stdin)
print(f'default_branch: {p[\"default_branch\"]}')
print(f'merge_method: {p[\"merge_method\"]}')
print(f'squash_option: {p[\"squash_option\"]}')
"

# Get branch protection
glab api projects/:id/protected_branches | python3 -c "
import sys,json
for b in json.load(sys.stdin):
    push = b['push_access_levels'][0]['access_level_description'] if b['push_access_levels'] else '?'
    merge = b['merge_access_levels'][0]['access_level_description'] if b['merge_access_levels'] else '?'
    print(f'{b[\"name\"]}: push={push}, merge={merge}, force_push={b[\"allow_force_push\"]}')
"
```

Update BRANCHES.md with the results.
