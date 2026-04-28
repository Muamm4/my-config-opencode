# Context Management Tools

Tools available for context management with usage patterns.

## Available Tools

### ctx_reduce
Drop tagged content by §N§ identifiers.

```typescript
// Single tags
ctx_reduce(drop="3")

// Ranges
ctx_reduce(drop="3-5")

// Multiple
ctx_reduce(drop="1,2,9")

// Combined
ctx_reduce(drop="3-5,8,10-12")
```

**When to use:**
- After extracting value from tool outputs
- Post-synthesis of background results
- When context > 70%

### ctx_expand
Decompress compartment ranges to see original transcript.

```typescript
// Decompress range
ctx_expand(start=1, end=10)
```

**When to use:**
- Need to recall full conversation for a compartment
- Lost context on earlier decisions

### ctx_note
Short durable notes for session continuity.

```typescript
// Write note
ctx_note(action="write", content="Implement X because Y")

// Read notes
ctx_note(action="read")

// Dismiss note
ctx_note(action="dismiss", note_id=1)
```

**When to use:**
- Deferring work for later
- Saving decisions that shouldn't be dropped

### Context Percentage Indicator
The system prompt shows context percentage. Monitor it continuously.

## Best Practices

1. **Targeted drops** over large ranges: `"3-5"` not `"1-50"`
2. **Immediate drops** after extraction: Don't hold onto processed outputs
3. **Selective drops**: Keep recent context, drop old content