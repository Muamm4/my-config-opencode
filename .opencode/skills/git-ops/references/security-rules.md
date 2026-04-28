# Security Rules

## Never Commit Secrets

### Critical Rule

**NEVER commit files containing credentials, secrets, or sensitive data.**

Files that MUST never be committed:
- `.env` files
- `credentials.json`
- `*.pem`, `*.key` (private keys)
- `secrets.yaml`
- API keys, tokens, passwords
- Database connection strings with credentials
- AWS/Azure/GCP credentials

## How to Prevent Accidental Commits

### 1. Gitignore

Ensure these are in `.gitignore`:

```
.env
.env.local
.env.*.local
credentials.json
*.pem
*.key
secrets.yaml
config/secrets/*
```

### 2. Pre-Commit Hooks

The skill enforces pre-commit hooks. Do NOT skip them unless explicitly requested.

### 3. git add --cached

For already-tracked files with secrets:

```bash
# Remove from staging (keeps file locally)
git rm --cached <file>

# Or unstage
git reset HEAD <file>
```

## If You Accidentally Committed Secrets

### Immediate Action

1. **Remove from history**:
   ```bash
   # Uses git filter-repo or BFG Repo-Cleaner
   bfg --delete-files <file>
   git reflog && git gc --prune=now --aggressive
   ```

2. **Rotate all credentials**: Consider all secrets compromised

3. **Push with force**:
   ```bash
   git push --force
   ```

### Prevention for Future

Add to `.git/hooks/pre-commit`:

```bash
#!/bin/bash
if git diff --cached --name-only | grep -E '\.(env|key|pem)$' > /dev/null; then
  echo "ERROR: Attempting to commit secret files"
  exit 1
fi
```

## Sensitive Data Patterns

Watch for these in code:

```javascript
// DON'T
const API_KEY = "sk-1234567890abcdef"

// DO
const API_KEY = process.env.API_KEY
```

```python
# DON'T
SECRET = "my-password"

# DO
import os
SECRET = os.environ.get("SECRET")
```