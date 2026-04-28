# Design Constraints

Fundamental principles that guide design decisions and prevent common pitfalls.

## Core Constraints

### 1. Never Sacrifice Usability for Aesthetics

**Rationale**: A beautiful interface that doesn't work is useless. Function always precedes form.

**Application:**
- If a visual effect compromises readability, remove it
- If an animation delays task completion, remove or speed it up
- If decoration distracts from content, remove it
- Always test the interface in its "working" state, not just its "showcase" state

**Examples:**
- ❌ Beautiful but tiny buttons that are hard to tap
- ❌ Glassmorphism that makes text unreadable
- ❌ Excessive animations that slow down interaction
- ✅ Clean, clear interfaces that look good AND work well

### 2. Avoid Cluttered Interfaces

**Rationale**: Clutter creates cognitive load and anxiety. Whitespace reduces cognitive burden.

**Application:**
- Prioritize whitespace over density
- Each element should have a clear purpose
- Limit visible options per screen (use progressive disclosure)
- Trust users to dig deeper if they need more

**Guidelines:**
- Minimum 16px padding between unrelated elements
- Maximum 5-7 visible options per group
- Use "see more" or "advanced" for less common options
- Every element should pass the "so what?" test

### 3. Ensure Consistent Iconography and Visual Language

**Rationale**: Inconsistency creates confusion. Users learn patterns; broken patterns frustrate.

**Application:**
- Use a consistent icon system (pick one icon library)
- Match visual treatment (stroke width, style, size)
- Use color semantically (same color = same meaning)
- Apply spacing and padding consistently

**Checklist:**
- [ ] Icons from the same family/library
- [ ] Consistent stroke weights
- [ ] Consistent sizing (use a scale)
- [ ] Consistent button heights
- [ ] Consistent padding values
- [ ] Consistent color usage

## Decision Framework

When in doubt, apply this decision tree:

```
1. Does this element help the user accomplish their goal?
   → Yes: Keep it
   → No: Remove it

2. Does this visual treatment enhance or hinder usability?
   → Enhances: Keep it
   → Hinders: Simplify or remove

3. Is this consistent with the rest of the interface?
   → Yes: Keep it
   → No: Make it consistent

4. Does this follow accessibility standards?
   → Yes: Keep it
   → Fix before releasing
```

## Anti-Patterns to Avoid

1. **Decoration Over Content**: Adding visual elements that don't serve a function
2. **False Urgency**: Using urgency tactics that don't reflect reality
3. **Dark Patterns**: Making it hard to undo or find simple options
4. **Inconsistent States**: Interactive elements that behave differently
5. **Missing Feedback**: Actions without visible response
6. **Broken Mental Models**: Familiar patterns that behave unexpectedly