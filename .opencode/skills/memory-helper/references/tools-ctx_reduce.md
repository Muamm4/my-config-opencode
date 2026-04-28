# ctx_reduce - Context Reduction

Mark old content for removal to manage context pressure. Drops stay queued until cache expires.

## When to Use

- When context usage is high
- After completing major milestones
- When old tool outputs are no longer needed
- To shed weight from compressed ranges

## Syntax

```python
# Drop specific tagged content
ctx_reduce(drop="3-5,12")

# Ranges: "3-5" drops tags 3, 4, and 5
# Multiple ranges: "1,2,9" drops tags 1, 2, and 9
# Combined: "1-5,8,12-15"
```

## How It Works

1. Drop requests are queued
2. They take effect when cache expires
3. Protected tags are accepted but deferred until they leave protected range

## Context Pressure System

- As context usage grows, the system sends reminders suggesting reduction
- Reminders become more frequent as usage increases
- If agent recently called `ctx_reduce`, reminders are suppressed
- **Emergency trigger at 80%** - always fires

## Guidelines

- Use after processing large tool outputs you no longer need
- Don't blindly drop - only content you've already acted on
- Protected recent context - avoid dropping anything from last ~20 messages