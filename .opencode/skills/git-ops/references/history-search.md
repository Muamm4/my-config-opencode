# History Search

## Git Blame

Find who wrote each line in a file.

```bash
git blame <file>
git blame -L <start>,<end> <file>  # Specific lines
git blame -w                      # Ignore whitespace changes
git blame -M                       # Ignore moves
```

### Example
```
git blame src/auth.js
a1b2c3d4 (John 2024-01-15 10:30) 1 | import { auth } from './auth'
a1b2c3d4 (John 2024-01-15 10:30) 2 | 
e5f6g7h8 (Jane 2024-01-16 14:22) 3 | export function login() {
```

### Using Blame Responsibly

- Use to find who can explain code context
- DON'T use to blame or shame
- Pair with `git log` to understand the full story

## Git Log Search

### Find Commits That Introduced a Pattern

```bash
git log -S "<pattern>" --oneline        # Pattern in diff
git log -S "<pattern>" --oneline -p      # With patch
git log -G "<regex>" --oneline           # Regex in patch
```

### Example
```
# Find when "useAuth" was added
git log -S "useAuth" --oneline

# Find when password validation was added
git log -S "password.*min" --oneline -p
```

### Use Cases

- Find when a bug was introduced
- Find when a feature was added
- Find who worked on specific functionality

## Log Shortcuts

| Command | Description |
|---------|------------|
| `git log --oneline -20` | Recent commits (compact) |
| `git log --graph --oneline --all` | Visual branch history |
| `git log --author="Name"` | Filter by author |
| `git log --since="2024-01"` | Filter by date |
| `git log --after="2024-01-01"` | Filter by date |
| `git log --grep="keyword"` | Search commit messages |

## Advanced Search

### Find Merge Commits
```bash
git log --merges --oneline
```

### Find Commits That Touched Specific Files
```bash
git log --follow --oneline <file>
```

### Find Biggest Commits
```bash
git log --pretty=format:%h --shortstat | ...
```

## Finding Lost Commits

```bash
git reflog                    # Your recent actions
git fsck --lost-found         # Unreachable commits
git log --all --oneline --graph | grep <keyword>
```