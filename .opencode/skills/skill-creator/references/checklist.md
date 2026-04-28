# Skill Quality Checklist

Use this checklist to verify a new skill before it is deployed.

## 1. Architecture Check
- [ ] Does it use the **Discovery Stub** pattern for complex domains?
- [ ] Is the `SKILL.md` concise and acting as a router?
- [ ] Are reference files atomic and focused on specific topics?
- [ ] Is there a clear Knowledge Map in the main file?

## 2. Identity & Persona Check
- [ ] Is the identity specific and authoritative?
- [ ] Does it define a clear goal and a set of priorities?
- [ ] Is the persona distinct from a general-purpose assistant?

## 3. Operational Check
- [ ] Are instructions actionable and sequence-oriented?
- [ ] Are tool usages explicitly defined?
- [ ] Is there a clear "Workflow" section?
- [ ] Are there "Anti-Patterns" or "Forbidden Actions" listed?

## 4. Trigger Check
- [ ] Are trigger conditions specific enough to avoid conflicts?
- [ ] Would a human know exactly when to activate this skill?

## 5. Technical Check
- [ ] Are all code examples up-to-date and syntactically correct?
- [ ] Are the tool names accurate?
- [ ] Does the skill avoid generic advice (e.g., "be helpful")?
