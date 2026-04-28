# ctx_expand - Decompress History

Expand compressed history ranges back to full transcript. Used to retrieve the full conversation context around a specific result.

## When to Use

- After `ctx_search` returns results - use ordinal to expand context
- When you need the full conversation around a compressed range
- To review what was discussed in a specific message range

## Syntax

```python
# Expand a range of messages
ctx_expand(start=100, end=200)

# Parameters
# - start: Starting message ordinal (inclusive)
# - end: Ending message ordinal (inclusive)
```

## How It Works

1. Compressed history stores summaries, not full transcripts
2. When you need the full conversation, `ctx_expand` restores it
3. Useful after `ctx_search` results to see the full context
4. Capped at ~15K tokens to prevent excessive output

## Use with ctx_search

```python
# 1. Search for relevant context
ctx_search(query="oauth authentication")

# 2. Use ordinal from result to expand
ctx_expand(start=100, end=150)

# This gives you the full conversation where "oauth authentication" was discussed
```

## Guidelines

- Use after getting search results to see full context
- Don't expand blindly - only ranges you're actively investigating
- Expansions consume tokens, use judiciously