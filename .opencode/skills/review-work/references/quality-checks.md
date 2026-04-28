# Quality Checks - Code Standards

This reference defines the quality standards every implementation must meet.

## Type Safety Requirements

**NEVER allow:**
- `as any` type assertions
- `@ts-ignore` comments
- `@ts-expect-error` without documented reason

**ALWAYS prefer:**
- Proper TypeScript types
- Explicit interfaces/types for complex objects
- Type inference where obvious

## Error Handling Requirements

**NEVER allow:**
- Empty catch blocks: `catch (e) { }`
- Silent failures without logging

**ALWAYS require:**
- Meaningful error handling
- Error logging or user feedback
- Graceful degradation where appropriate

## AI Slop Detection

**Remove unnecessary patterns:**
- Verbose comments explaining obvious code
- Excessive documentation for simple functions
- Unnecessary abstractions
- Over-commenting (code should be self-documenting)

**Keep:**
- Non-obvious business logic explanations
- Complex algorithm comments
- API contract documentation

## Codebase Conventions

**Must follow:**
- Existing naming conventions (camelCase, PascalCase, etc.)
- File organization patterns
- Import ordering (standard library → external → internal)
- Error handling patterns used in the codebase
- Testing patterns (if tests exist)

## Quick Checklist

| Check | Status |
|-------|--------|
| No `as any` | [ ] |
| No `@ts-ignore` | [ ] |
| No empty catch blocks | [ ] |
| No AI slop patterns | [ ] |
| Follows conventions | [ ] |

## Common Pitfalls

1. **Type coercion**: Using `as` instead of proper typing
2. **Silent failures**: Catching errors and doing nothing
3. **Over-abstraction**: Creating interfaces for simple data
4. **Copy-paste**: Not adapting to codebase patterns