# Force Push Safety

## Golden Rules

1. **NEVER force push to main/master** without explicit user consent
2. **ALWAYS warn** before force push
3. **NEVER amend** pushed commits

## Why Force Push is Dangerous

```
# Your local is behind
o---o---o---A---B---C (your branch)
o---o---o---A---D---E (remote)

# force push replaces E with C
# D is LOST FOREVER
```

Force push **rewrites** remote history. Any commits not in your local are deleted.

## When Force Push is Acceptable

- Cleaning up local feature branch before first push
- After `git rebase -i` on local commits
- Removing accidental commits from remote
- ** NEVER on shared branches **

## Safety Protocol

### Before Any Force Push

1. **Check current state**:
   ```bash
   git status
   git log --oneline -10
   git fetch && git log HEAD..origin/main --oneline
   ```

2. **Show user what will happen**:
   - Run `git log` showing commits that will be lost
   - Explain the impact

3. **Ask explicit permission**:
   ```
   "This will replace remote commits with [X]. Are you sure?"
   ```

### Safer Alternative: --force-with-lease

```bash
git push --force-with-lease
```

This fails if someone else pushed (protects against overwriting others' work).

```bash
# With lease + trace
git push --force-with-lease --force-if-includes
```

This additionally checks your local knows about all remote refs.

## Emergency Recovery

If you accidentally force pushed:

1. **Find the commit**:
   ```bash
   git reflog
   git fsck --lost-found
   ```

2. **Recover**:
   ```bash
   git reset --hard <commit>
   git push --force
   ```

## Commands Reference

| Command | Use Case |
|---------|---------|
| `git push --force` | Dangerous - replaces remote entirely |
| `git push --force-with-lease` | Safer - fails if remote changed |
| `git push --force-if-includes` | Safest - checks refs |
| `git push --force-with-lease --force-if-includes` | Maximum safety |