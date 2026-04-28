# ctx_memory - Persistent Memories

Long-term memories that survive across sessions. Use for important info that should persist beyond the current session.

## When to Use

- Important technical decisions that should persist
- Bug workarounds discovered during investigation
- Project context that spans sessions
- Environment configurations
- Workflow rules that the agent should remember

## Syntax

```python
# Write a persistent memory
ctx_memory(action="write", category="DEV", content="Fix: stickers were not appearing...")

# Delete a memory by ID
ctx_memory(action="delete", id=42)

# List memories
ctx_memory(action="list")

# List with filter
ctx_memory(action="list", category="DEV")
ctx_memory(action="list", limit=20)

# Update existing memory
ctx_memory(action="update", id=42, content="Updated content...")

# Merge memories
ctx_memory(action="merge", ids=[1, 2, 3], category="MERGED")

# Archive old memories
ctx_memory(action="archive", id=42, reason="No longer relevant")
```

## Categories

Use categories to organize memories logically:

| Category | Use Case |
|----------|----------|
| `DEV` | Development notes, bug fixes, code changes |
| `CONSTRAINTS` | Technical constraints, limitations |
| `ENVIRONMENT` | Environment setup, paths, configurations |
| `WORKFLOW_RULES` | How to perform certain tasks |
| `ARCHITECTURE_DECISIONS` | Design decisions, patterns chosen |

## Guidelines

- Use `ctx_memory` for information that should survive session restarts
- Categorize properly for easier retrieval
- Prefer `ctx_note` for temporary/short-term information
- Largememories are automatically injected into new sessions