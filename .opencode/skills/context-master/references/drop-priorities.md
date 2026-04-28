# Drop Priorities

What to automatically drop vs. what to NEVER drop.

## Auto-Drop (High Priority)

Drop these immediately after **summarizing and storing** their value:

| Content Type | Drop Trigger |
|-------------|-------------|
| Large file reads | After summarizing critical parts and acting on content |
| Old grep/search results | After summarizing patterns and synthesis complete |
| Background agent raw outputs | After summarizing findings and storing in memory |
| Session messages (§N§ tagged) | After summarizing key decisions and fully processed |
| Build logs, diagnostics | After summarizing root causes and errors addressed |

### Examples

```typescript
// After reading a file and implementing
read(filePath="large-file.ts")
→ Implement changes
→ ctx_reduce(drop="1-3") // drop the file read output

// After grep search processed
grep(pattern="...")
→ Use results
→ ctx_reduce(drop="5-8") // drop grep results
```

## NEVER Drop (Protected)

These must NEVER be dropped:

| Content Type | Reason |
|-------------|--------|
| User messages | Cheap, summarized automatically |
| Your conversation text | Lightweight |
| Current todo list | Task tracking |
| Active task context | Current work |
| Recent errors | Unresolved decisions |
| Task requirements | Initial prompt |

## The Rule

**If you extracted value from it → Summarize → Store → Drop it.**

If the content was a means to an end and that end is achieved → summarize the result and drop the means.