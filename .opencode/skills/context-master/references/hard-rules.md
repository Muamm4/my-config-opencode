# Hard Rules for Context Reduction

Non-negotiable rules. When these conditions are met, reduction is MANDATORY.

## The Rules

### Rule 1: Context > 70% (Dynamic)
**Condition:** Context exceeds 70% (Lower to 60% for complex architecture tasks)
**Action:** Start being aggressive with reductions, ensuring critical info is summarized first.

```
When context > 70% (or dynamic threshold):
→ Summarize critical context → Store in ctx_memory/ctx_note
→ ctx_reduce(drop="old-unneeded-outputs")
→ Prioritize dropping large tool outputs
→ Keep only critical context
```

### Rule 2: Context > 85% (Dynamic)
**Condition:** Context exceeds 85% (Lower to 75% for high-complexity tasks)
**Action:** MUST reduce before continuing ANY work.

```
When context > 85% (or dynamic threshold):
→ STOP all work immediately
→ Summarize critical state → Store in ctx_note
→ ctx_reduce(drop="...") until < 70%
→ THEN resume work
```

### Rule 3: After Multi-Step Task
**Condition:** Any task with 3+ steps completes
**Action:** Reduce immediately

```
Task completion (todowrite all done):
→ ctx_reduce(drop="relevant-tags")
→ Free up space for next task
```

### Rule 4: Before Complex Work
**Condition:** About to start multi-agent or complex task
**Action:** Ensure headroom (< 60%)

```
Before complex task (e.g., background agents):
→ If context > 60%:
→   Reduce first
→ Then proceed
```

## Hard Boundaries

| Threshold | Behavior | Dynamic Adjustment |
|-----------|----------|-------------------|
| 70% | Warning - start aggressive reduction | Lower to 60% for complex tasks |
| 85% | HARD STOP - no work until reduced | Lower to 75% for high-complexity |
| Any multi-step complete | Mandatory reduction | N/A |

## Anti-Patterns

- ❌ Waiting until 90%+ to reduce
- ❌ Continuing work when > 85%
- ❌ Batch-completing todos without reducing between
- ❌ Not checking context percentage

## The Enforced Pattern

```
Check context → If threshold met → Reduce → Then work
```

No exceptions. No negotiation.