# ctx_note - Session Notes

Session notes exist only in the current session. They are the primary tool for capturing transient information during development.

## When to Use

- **Frequently** during active development
- Capturing temporary reminders
- Saving workarounds found during bug investigation
- Recording commands that work
- Storing file paths for quick access

## Syntax

```python
# Write a session note
ctx_note(action="write", content="User prefers React over Vue")

# Read all session notes
ctx_note(action="read")

# Read with filters
ctx_note(action="read", filter="all")  # All statuses
ctx_note(action="read", filter="active")  # Active + ready smart notes (default)
ctx_note(action="read", filter="pending")  # Unsurfaced smart notes
```

## Smart Notes

Create project-scoped notes that surface when conditions are met:

```python
ctx_note(
    action="write",
    content="Implement X because Y",
    surface_condition="When PR #42 is merged in this repo"
)
```

## Guidelines

- **Write freely** - session notes are cheap and persist in session
- **No need to ask** - just save proactively
- **Great for temporary info** - file paths, working commands, quick reminders
- **Short-term only** - if info needs to survive sessions, use `ctx_memory`