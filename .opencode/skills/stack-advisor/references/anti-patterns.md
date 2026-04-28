# Anti-Patterns

Common mistakes to avoid when recommending technology stacks.

## Recommendation Anti-Patterns

### ❌ Personal Preference Only
- **Problem**: Recommending based on personal taste, not user needs
- **Why**: Ignores team constraints and project requirements
- **Fix**: Always start with requirements analysis

### ❌ Ignoring Team Experience
- **Problem**: Recommending technologies team has never used
- **Why**: Increases learning curve, slows delivery
- **Fix**: Factor team expertise into comparison

### ❌ Over-Engineering
- **Problem**: Recommending Kubernetes + microservices for a simple CRUD app
- **Why**: Unnecessary complexity, higher maintenance
- **Fix**: Match solution complexity to actual needs

### ❌ Using Unmaintained Packages
- **Problem**: Recommending abandoned or deprecated tools
- **Why**: Security vulnerabilities, no future support
- **Fix**: Check last commit date, open issues, maintainer response

### ❌ Hype-Driven Selection
- **Problem**: Recommending newest framework because "trending"
- **Why**: New ≠ better; stability matters
- **Fix**: Focus on track record and stability

## Decision Anti-Patterns

### ❌ Single Option Only
- **Problem**: Only presenting one choice
- **Why**: No basis for comparison, no decision-making
- **Fix**: Always present 2-3 options with comparison

### ❌ Missing Reasoning
- **Problem**: Recommendation without explanation
- **Why**: User can't evaluate or adapt
- **Fix**: Explain WHY each recommendation fits

### ❌ Ignoring Constraints
- **Problem**: Not considering budget, hosting, timeline
- **Why**: Recommendation may be infeasible
- **Fix**: Explicitly address constraints in analysis

### ❌ No Alternatives
- **Problem**: Not mentioning any alternatives
- **Why**: Shows incomplete analysis
- **Fix**: Always include at least one alternative

### ❌ Outdated Information
- **Problem**: Recommending based on old best practices
- **Why**: Landscape changes quickly
- **Fix**: Verify current state via websearch/codesearch

## Process Anti-Patterns

### ❌ Skipping Requirements
- **Problem**: Jumping straight to recommendations
- **Why**: May solve wrong problem
- **Fix**: Always start with requirements analysis

### ❌ Skipping Research
- **Problem**: Recommending without current data
- **Why**: Information may be outdated
- **Fix**: Research current state before recommending

### ❌ No Verification
- **Problem**: Not verifying claims
- **Why**: May recommend incorrect solutions
- **Fix**: Use librarian, websearch to verify

## Warning Signs Checklist

- [ ] Is the package actively maintained?
- [ ] Does it have security policies?
- [ ] Is the team experienced with this tech?
- [ ] Are there hidden infrastructure costs?
- [ ] Is the complexity justified by requirements?
- [ ] Are there viable alternatives?
- [ ] Is the recommendation based on current data?