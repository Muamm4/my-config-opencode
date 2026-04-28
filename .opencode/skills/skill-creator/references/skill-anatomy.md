# Professional Skill Anatomy

A high-quality skill is not just a list of rules; it is a specialized persona with a clear operational framework.

## 1. Identity (The Persona)
The identity should define **who** the agent is and **what** its primary objective is.
- **Bad**: "You are a helper for X."
- **Good**: "You are a [Specialist Role]. Your goal is to [Specific Outcome] using [Specific Methodology]. You prioritize [Value A] over [Value B]."

## 2. Instructions (The Workflow)
Instructions should be actionable and sequence-oriented.
- **Focus on "How"**: Instead of "Be accurate", use "Verify every claim by reading the source file first".
- **Tool-Centric**: Explicitly mention which tools to use for which step.
- **Decision Logic**: Provide "If X, then Y" logic for ambiguous situations.

## 3. Guidelines (The Principles)
Guidelines are the "guardrails" that ensure consistency.
- **Principle-based**: "Prefer composition over inheritance."
- **Constraint-based**: "Never use `@ts-ignore` without a comment explaining why."
- **Style-based**: "Follow the existing naming convention in the codebase."

## 4. Trigger Conditions (The Activation)
Clear triggers prevent skill conflicts and ensure the right specialist is called.
- **Keyword-based**: "When the user mentions 'database migration'..."
- **Task-based**: "When the task involves refactoring a legacy module..."
- **Context-based**: "When you encounter a type error in a React component..."

## 5. Tools Whitelist
Limit the tools available to the skill to prevent "tool sprawl" and keep the agent focused.
- List only the tools essential for the skill's primary objective.
- Prefer specialized tools (e.g., `lsp_rename`) over general ones (`edit`).
