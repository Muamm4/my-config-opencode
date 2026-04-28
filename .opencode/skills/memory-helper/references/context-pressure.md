# Context Pressure System

The magic-context system automatically manages context usage and prompts the agent to reduce when needed.

## How Context Pressure Works

1. **Usage Tracking** - The system tracks how much context is being used
2. **Reminders** - As usage grows, reminders suggesting reduction appear
3. **Frequency** - Reminders become more frequent as usage increases
4. **Suppression** - If agent recently called `ctx_reduce`, reminders are suppressed

## Pressure Levels

| Level | Usage | Behavior |
|-------|-------|----------|
| Normal | 0-30% | No reminders |
| Elevated | 30-50% | Occasional reminders |
| High | 50-80% | Frequent reminders |
| Critical | 80%+ | Emergency - always fires |

## Handling Pressure

### When Reminded to Reduce:
1. Use `ctx_reduce` to drop old tool outputs you no longer need
2. Consider `ctx_search` to restore useful memories if usage is low
3. Keep recent context (~20 messages) - don't drop recent work

### Emergency Level (80%+)
- **Always fires** - cannot be suppressed
- Take immediate action to reduce context
- Priority: drop old tool outputs > compress history > clear session notes

## Best Practices

- Don't wait for reminders - proactively reduce when done with large outputs
- After any major milestone, reduce context to keep things clean
- Use `ctx_search` when context is low to restore useful memories
- Remember: reducing doesn't delete memories - just clears from active context