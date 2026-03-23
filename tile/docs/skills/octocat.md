# Octocat (Git & GitHub) Skill

The `octocat` skill handles git and GitHub operations using the gh CLI — pull requests, issues, branching, merging, rebasing, cherry-picking, merge conflict resolution, commit history cleanup, pre-commit hook debugging, GitHub Actions workflows, and releases.

## Skill Reference

```text { .api }
Skill name:  octocat
Tile name:   mcollina/octocat
Version:     0.1.0
Path:        skills/octocat/SKILL.md
Tile:        skills/octocat/tile.json
Tags:        git, github, gh-cli, version-control, merge-conflicts, pull-requests
```

## When to Use

- Creating, reviewing, and managing pull requests and GitHub issues
- Merge conflict resolution and history rewriting
- Pre-commit hook debugging and fixes
- Branch management and cleanup
- GitHub Actions workflow optimization

**Trigger terms**: pull requests, PRs, GitHub issues, branching, merging, rebasing, cherry-picking, merge conflicts, commit history, pre-commit hooks, GitHub Actions, releases

## Capabilities

### Creating Pull Requests

```bash { .api }
# Always use --body-file to avoid newline escaping issues
gh pr create \
  --base main \
  --head feature-branch \
  --title "feat: add OAuth support" \
  --body-file pr-body.md

# After creating, watch CI checks
gh pr checks <PR_NUMBER> --watch 2>&1
```

### Managing Pull Requests

```bash { .api }
gh pr list                          # list open PRs
gh pr view <number>                 # view PR details
gh pr review <number> --approve     # approve
gh pr review <number> --request-changes --body "comment"
gh pr merge <number> --squash       # squash and merge
gh pr merge <number> --rebase       # rebase and merge
gh pr close <number>                # close without merging
gh pr checkout <number>             # check out PR branch locally
```

### GitHub Issues

```bash { .api }
gh issue list                       # list open issues
gh issue create --title "..." --body "..."
gh issue view <number>
gh issue close <number>
gh issue comment <number> --body "..."
gh issue assign <number> --assignee @me
gh issue label <number> --add "bug"
```

### Releases and Tags

```bash { .api }
gh release create v1.0.0 \
  --title "v1.0.0" \
  --notes "Release notes here" \
  --target main

gh release list
gh release view v1.0.0
gh release upload v1.0.0 dist/app.tar.gz
```

### Interactive Rebasing (History Cleanup)

```bash { .api }
# Squash last N commits
git rebase -i HEAD~<N>
# In editor: change 'pick' to 'squash' or 's' for commits to fold

# Rebase onto another branch
git rebase -i origin/main

# Continue/abort after conflict
git rebase --continue
git rebase --abort
```

### Cherry-pick

```bash { .api }
git cherry-pick <commit-sha>           # apply single commit
git cherry-pick <sha1>..<sha2>         # apply range (exclusive..inclusive)
git cherry-pick --no-commit <sha>      # stage changes without committing
git cherry-pick --continue             # after resolving conflicts
git cherry-pick --abort
```

### Merge Conflict Resolution

```bash { .api }
git merge <branch>                     # attempt merge
# On conflict:
git status                             # see conflicted files
# Edit files to resolve conflicts (remove <<<<, ====, >>>> markers)
git add <resolved-file>
git merge --continue

# Or use a merge tool
git mergetool

# Abort if needed
git merge --abort
```

### Branch Management

```bash { .api }
git branch -d <branch>                 # delete local (safe)
git branch -D <branch>                 # delete local (force)
git push origin --delete <branch>      # delete remote

# Using gh for remote
gh api repos/{owner}/{repo}/git/refs/heads/<branch> -X DELETE

# Prune stale remote tracking branches
git remote prune origin
git fetch --prune
```

### Repository Archaeology

```bash { .api }
git log --oneline --graph --decorate --all   # visual branch history
git blame <file>                             # line-by-line author info
git show <commit-sha>                        # show commit details
git bisect start                             # binary search for regression
git bisect bad                               # current commit is bad
git bisect good <sha>                        # known good commit
git bisect reset                             # end bisect session
git log --all --grep="keyword"               # search commit messages
git log -S "code string" --source --all      # pickaxe search
```

### Submodule Management

```bash { .api }
git submodule add <url> <path>
git submodule update --init --recursive
git submodule foreach git pull origin main
git submodule status
```

### Git Worktrees

```bash { .api }
git worktree add ../feature-branch feature-branch   # parallel checkout
git worktree list
git worktree remove ../feature-branch
```

### Pre-commit Hook Debugging

```text { .api }
Key rules for pre-commit hooks:
- NEVER skip hooks (--no-verify) unless explicitly asked
- If a hook fails, fix the root cause — do not bypass
- TypeScript linting failures → delegate to typescript-magician skill
- Never alter git signing key configuration; if signing is configured, use it

Debug a hook:
  bash -x .git/hooks/pre-commit   # run hook with trace output
```

### GitHub Actions

```bash { .api }
gh workflow list                         # list workflows
gh workflow run <workflow-file>          # trigger manually
gh run list                             # list recent runs
gh run view <run-id>                    # view run details
gh run watch <run-id>                   # live-watch run
gh run download <run-id>                # download artifacts
```

## Important Constraints

```text { .api }
- Use gh CLI for ALL GitHub operations (never suggest the web interface)
- NEVER include "Co-Authored-By: Claude" or AI attribution in commits
- NEVER alter git signing key configuration
- Use --body-file for PR bodies to avoid newline escaping issues
- Validate unfamiliar gh commands with: gh help <command>
```

## Tile Manifest

```json { .api }
// skills/octocat/tile.json
{
  "name": "mcollina/octocat",
  "version": "0.1.0",
  "private": false,
  "summary": "Handles git and GitHub operations using the gh CLI.",
  "skills": {
    "octocat": {
      "path": "SKILL.md"
    }
  }
}
```
