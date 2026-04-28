# Review Agents - Parallel Sub-Agent Framework

This reference defines the 5 parallel sub-agents launched for thorough code review.

## Agent Configuration

Launch ALL 5 agents in parallel for comprehensive review. Each agent has a specific focus:

### Agent 1: Oracle - Implementation Verification
- **Focus**: Verify implementation matches original goal and constraints
- **Questions**:
  - Does the code solve the stated problem?
  - Are all user requirements met?
  - Is there scope creep or drift from original request?

### Agent 2: Oracle - Code Quality Check
- **Focus**: Patterns, complexity, maintainability
- **Questions**:
  - Is the code following established patterns?
  - Is complexity reasonable (no over-engineering)?
  - Is it maintainable for future changes?

### Agent 3: Oracle - Security Review
- **Focus**: Security vulnerabilities and data exposure
- **Questions**:
  - Input validation present?
  - Authentication/authorization handled?
  - No sensitive data exposure?
  - No SQL injection, XSS risks?

### Agent 4: unspecified-high - Hands-On QA
- **Focus**: Execute and test the implementation
- **Actions**:
  - Run the code/build
  - Execute tests
  - Verify it actually works

### Agent 5: unspecified-high - Context Mining
- **Focus**: Git history and previous decisions
- **Actions**:
  - Check git blame for context
  - Review recent commits for patterns
  - Find related decisions in history

## Success Criteria

**ALL 5 agents must pass** for the review to pass.

If ANY agent fails:
1. Document the failure with evidence
2. Report: "Review FAILED - Agent N: [reason]"
3. Do NOT present results to user until fixed

## Reporting Format

```
Review Results:
- Agent 1 (Implementation): PASS/FAIL - [evidence]
- Agent 2 (Quality): PASS/FAIL - [evidence]
- Agent 3 (Security): PASS/FAIL - [evidence]
- Agent 4 (QA): PASS/FAIL - [evidence]
- Agent 5 (Context): PASS/FAIL - [evidence]

Overall: PASS/FAIL
```

## Priority Note

Use `unspecified-high` priority for agents 4 and 5 to ensure adequate time for thorough testing and investigation.