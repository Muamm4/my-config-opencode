# Commit Messages

## Convention: Conventional Commits

Format: `<type>: <description>`

```
<type>(<scope>): <description>

[optional body]
[optional footer]
```

## Types

| Type | When to Use |
|------|-------------|
| feat | New feature for user |
| fix | Bug fix for user |
| refactor | Code change that neither fixes nor adds feature |
| docs | Documentation only |
| test | Adding or updating tests |
| chore | Maintenance tasks, deps, tooling |
| style | Formatting, no code change |
| perf | Performance improvement |

## Rules

1. **Use imperative mood**: "add" not "added" or "adds"
2. **Lowercase type**: always lowercase
3. **No period after subject line**
4. **Max 50 chars** for subject line
5. **Separate body with blank line** if used

## Examples

### Good
```
feat: add user authentication flow

Implements JWT-based auth with refresh tokens.
- Login endpoint returns access + refresh tokens
- Refresh rotates token pair
- Logout invalidates refresh token

Closes #123
```

### Good (simple)
```
fix: resolve null pointer in user profile
```

### Bad
```
Added new feature X and fixed Y
```

### Bad
```
WIP: working on something
```

## Scope (Optional)

Use scope to indicate what component changed:

```
feat(auth): add password reset flow
fix(ui): resolve button alignment on mobile
refactor(api): extract validation to service
```

## Footer (Optional)

Use for referencing issues or breaking changes:

```
Closes #123
Fixes #456

BREAKING CHANGE: auth token now expires in 24h
```

## Tools

This skill uses git-master skill from Oh-My-OpenAgent. Let it handle commit message generation.