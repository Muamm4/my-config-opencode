# Auto-Reduce Triggers

These are the HARD TRIGGERS that require IMMEDIATE action. When any of these conditions are met, reduce context BEFORE continuing.

## Trigger Conditions

### 1. After Collecting Background Agent Results
```typescript
// After background_output returns → summarize → store → drop raw outputs immediately
background_output(task_id="...")
→ Summarize critical findings
→ Store in ctx_memory/ctx_note
→ drop raw output (use ctx_reduce)
```

### 2. After Delegation Results Verified
```typescript
// After explore/librarian results verified → summarize → store → drop full output
ctx_reduce(drop="3-5,8-10") // drop raw outputs, keep verification summary
```

### 3. After Completing Any Multi-Step Task
```typescript
// Task has multiple steps (todowrite showed 3+ items)
// When final step completes → reduce immediately
ctx_reduce(drop="relevant-tags")
```

### 4. When Context > 70%
```typescript
// Any time context exceeds 70%
// Start aggressive reductions
ctx_reduce(drop="old-tool-outputs")
```

### 5. Before Starting New Complex Task
```typescript
// About to start multi-step task
// Ensure < 60% context headroom
ctx_reduce(drop="unneeded-tags")
if (context > 60%) {
  // Reduce more before proceeding
}
```

## The Pattern

**Trigger → Summarize → Store → Drop Immediately**

1. **Trigger**: Condition met (e.g., Context > 70% or Task Complete).
2. **Summarize**: Extract critical insights, decisions, and patterns.
3. **Store**: Save to `ctx_memory` (permanent) or `ctx_note` (session).
4. **Drop**: Use `ctx_reduce` to remove raw outputs.

Never hold onto raw outputs after you've extracted and stored their value.