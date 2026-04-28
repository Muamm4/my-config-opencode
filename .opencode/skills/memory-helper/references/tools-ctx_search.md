# ctx_search - Unified Search

Search across ALL data sources with a single query - memories, facts, and conversation history.

## When to Use

- **Before answering questions** about past work
- **When context is low** - restore useful memories
- **User mentions specific project** - search for project context
- **First message of session** - establish project context

## Syntax

```python
# Search across all sources
ctx_search(query="authentication approach")

# Limit results
ctx_search(query="oauth authentication", limit=10)

# Use results with ctx_expand
# Each result includes an ordinal that can expand the full context
```

## What It Searches

- **Project memories** - long-term memories stored via ctx_memory
- **Session facts** - facts established in current session
- **Conversation history** - past messages in current session

## Results Format

Results are ranked by relevance and include:
- Relevance score
- Source (memory/fact/history)
- Ordinal for `ctx_expand` to get full context

## Guidelines

- **Always search before answering** questions about past work
- **Use search to restore context** when context usage is below 30%
- **First session message** - always search for project context first