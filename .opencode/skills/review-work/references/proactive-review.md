# Proactive Review - Auto-Activation Triggers

This reference defines when the review-work skill MUST run reviews WITHOUT being asked.

## Mandatory Auto-Activate Conditions

Run review checks automatically when ANY of these conditions are met:

### 1. After Multi-File Implementation
- **Trigger**: Any task that modified 2+ files
- **Action**: Run `lsp_diagnostics` + build verification

### 2. After Delegation Completes
- **Trigger**: Any `call_omo_agent` task returns results
- **Action**: Verify results before presenting to user

### 3. Before Final Answer on Complex Tasks
- **Trigger**: Task involves multiple steps (todo created)
- **Action**: Ensure all checks pass before confirming completion

### 4. User Signals Completion
- **Trigger**: User says "done", "finished", "complete", "all set"
- **Action**: Run review before confirming

## Task Complexity Classification

### Trivial Tasks (Skip Full Review)
- Single file change
- Typo fix, comment addition
- Quick sanity check only: `lsp_diagnostics` + "Looks good"

### Complex Tasks (Full Review Required)
- Takes > 5 minutes of agent time
- Involves 2+ files
- Multiple steps (todo list created)
- Implementation involves new patterns or logic

## Quick Reference

| Condition | Action |
|-----------|--------|
| Multi-file change | Full review |
| Delegation returns | Verify results |
| Complex task finish | All checks pass |
| User says "done" | Review before confirm |
| Single file, typo | Quick sanity only |

## Anti-Pattern

**DO NOT** ask "Should I run review?" - Just run it. The cost of unnecessary review is low; the cost of missed issues is high.