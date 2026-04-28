# Memory Integration - Context Preservation

This reference defines how to save review findings for future sessions.

## When to Save

**AFTER** review passes, save key decisions to `ctx_note`.

Do NOT save if review failed - the failure report is sufficient.

## What to Save

Save a concise summary with:
- **Files changed**: List of modified files
- **Tests**: Test results (passed/failed/count)
- **Pattern used**: Key patterns or approaches
- **Decisions**: Architectural or design decisions made

## Format

Use `ctx_note` with action="write":

```
Files changed: src/api/users.ts, tests/users.test.ts
Tests: 12 passed, 0 failed
Pattern: Repository pattern with dependency injection
Decision: Used async/await over .then() for readability
```

## Why This Matters

1. **Future context**: Future sessions can understand what was done
2. **Pattern tracking**: Build knowledge of what works in this codebase
3. **Debugging**: Easier to trace back decisions
4. **Continuity**: Better handover between sessions

## Anti-Patterns

**DO NOT** save:
- Failed review details (already in failure report)
- Excessive detail (keep it concise)
- Sensitive information (no API keys, credentials)

**DO** save:
- File paths and names
- Test outcomes
- Pattern names
- Key decisions

## Example Note

```typescript
ctx_note(action="write", content="Review passed for user-auth feature. Files: auth.ts, auth.test.ts. Pattern: JWT with refresh tokens. Tests: 8/8 passed.")
```

## Integration with Workflow

Memory integration is step 5 of Full Review (see verification-process.md):
1. LSP Diagnostics → 2. Build → 3. Tests → 4. Implementation Check → 5. **Memory Save** → 6. Report