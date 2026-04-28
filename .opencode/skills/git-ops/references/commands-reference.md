# Git Commands Reference

## Atomic Commit

```bash
# Stage specific files
git add <file1> <file2>

# Commit with message
git commit -m "feat: add X"

# Push
git push
```

## Amend (Your Own Commits Only)

```bash
# Amend last commit (if not pushed)
git commit --amend --no-edit

# Amend with new message
git commit --amend -m "feat: update message"
```

**Note**: NEVER amend pushed commits.

## Branch Operations

```bash
# Create new branch
git checkout -b <branch-name>

# Switch branch
git checkout <branch-name>

# Delete local branch
git branch -d <branch-name>

# Delete remote branch
git push origin --delete <branch-name>
```

## Rebase

```bash
# Rebase onto main
git rebase main

# Interactive rebase (last N commits)
git rebase -i HEAD~N

# Abort rebase
git rebase --abort

# Continue after resolving
git rebase --continue

# Skip problematic commit
git rebase --skip
```

## Stashing

```bash
# Stash changes
git stash
git stash push -m "message"

# Apply stash
git stash apply
git stash pop (apply + remove)

# List stashes
git stash list

# Drop stash
git stash drop <stash-id>
```

## Viewing History

```bash
# Compact log
git log --oneline

# With graph
git log --graph --oneline --all

# With diff
git log -p

# Single file history
git log --follow <file>
```

## Checking State

```bash
# Status
git status

# Differenced
git diff
git diff --staged
git diff <commit1> <commit2>

# What changed in commit
git show <commit>
```

## Undoing

```bash
# Unstage files
git reset HEAD <file>

# Discard changes
git checkout -- <file>

# Revert commit (creates new commit)
git revert <commit>

# Reset (dangerous)
git reset --soft HEAD~1   # Keep changes staged
git reset --mixed HEAD~1   # Keep changes unstaged (default)
git reset --hard HEAD~1    # Discard changes (dangerous)
```

## Fetching & Pulling

```bash
# Fetch all
git fetch --all

# Pull (fetch + merge)
git pull

# Pull with rebase
git pull --rebase
```