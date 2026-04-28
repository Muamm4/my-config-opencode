# Commit Strategy

## Atomic Commits

Each commit should represent **one logical change**. A good atomic commit:

- Is self-contained (can be reverted independently)
- Contains only related changes
- Has a clear purpose

### What makes a commit atomic?

| Non-Atomic | Atomic |
|-----------|--------|
| "Add feature X and refactor Y" | "feat: add feature X" / "refactor: simplify Y logic" |
| "Fix bug, update deps, add tests" | "fix: resolve Y issue" / "chore: update deps" / "test: add coverage for Y" |
| Multiple file types changed | Single concern per commit |

### Rule of Thumb

If your commit message contains "and", it's likely too large. Split it.

## Rebase vs Merge

**Prefer Rebase** over Merge for clean history.

```
# Merge (creates merge commit)
git checkout main
git merge feature-branch
# Result: "Merge branch 'feature' into main"

# Rebase (linear history)
git checkout feature-branch
git rebase main
# Result: Feature commits applied on top of main
```

### When to use Merge

- Pulling shared feature branches from collaborators
- When you need to preserve exact timestamps
- Public/shared branches where rebase would rewrite others' history

### When to use Rebase

- Before submitting a PR
- Updating your local feature branch with main
- Cleaning up local commits before push

## Interactive Rebase (Squashing)

Use `git rebase -i` to squash, edit, or reorder commits.

```bash
# Squash last 3 commits into one
git rebase -i HEAD~3
# In editor: mark commits as 'squash' or 's' (except first)
```

### Common Interactive Rebase Operations

| Command | Abbrev | Description |
|---------|-------|-------------|
| pick | p | Use commit as-is |
| reword | r | Change commit message |
| edit | e | Stop to amend commit |
| squash | s | Merge into previous commit |
| drop | d | Remove commit |