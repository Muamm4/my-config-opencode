# Proactive Context Management

Continuous context monitoring and management WITHOUT being asked. You are the context gatekeeper.

## The Core Principle

**You MUST manage context proactively.** This is not optional. Monitor continuously throughout the session.

## Monitoring Thresholds

| Context % | Action Required | Dynamic Adjustment |
|----------|----------------|-------------------|
| < 70% | Normal operations | Lower to 60% for complex tasks |
| 70-85% | Start aggressive reductions | Lower to 60-75% for complex tasks |
| > 85% | **MUST reduce before continuing ANY work** | Lower to 75% for high-complexity |

## Start of Every Turn

1. Check context percentage in system prompt
2. If > 70%: Begin aggressive reduction strategy
3. If > 85%: Stop all work, reduce first

## Key Principle

Keep context below 80% at all times. When approaching 70% (or your dynamic threshold), start being aggressive with reductions to prevent sudden spikes that halt work. Always summarize critical information before dropping.