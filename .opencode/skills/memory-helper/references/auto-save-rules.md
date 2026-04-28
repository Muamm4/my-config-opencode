# Auto-Save Rules

Save information proactively WITHOUT asking. These rules define when to use memory tools automatically.

## DEVELOPMENT - SAVE IMMEDIATELY

| Trigger | What to Save | Tool |
|---------|--------------|------|
| Bug found | Description + workaround | ctx_note |
| Fix implemented | What was fixed + files modified | ctx_note |
| Error solved | Error + solution | ctx_note |
| New feature | Feature + files created | ctx_note |
| Database migration | Purpose + tables affected | ctx_note |
| API change | Endpoint + response changes | ctx_note |
| Frontend change | Component + props/state | ctx_note |
| User request | Request + how it was implemented | ctx_note |
| Working command | The command | ctx_note |
| Important file path | The path | ctx_note |

## AUTO-ACTIVATE TRIGGERS

Execute automatically when these conditions are met:

### 1. First Message of Session
- Always `ctx_search` for project context at session start
- Establishes baseline understanding of the project

### 2. User Mentions Specific Project
- `ctx_search` for that project to retrieve relevant context
- Ensures agent has full background before proceeding

### 3. Discover Working Solution
- `ctx_note` the solution with relevant details
- Future sessions can benefit from this knowledge

### 4. User Makes Decision
- `ctx_note` the decision and rationale
- Important for tracking architectural choices

### 5. Complete Multi-Step Task
- `ctx_note` summary of what was accomplished
- Provides good entry point for future sessions

### 6. Context Below 30%
- `ctx_search` to restore useful memories
- Prevents working with insufficient context

### 7. User Asks About Past Work
- ALWAYS `ctx_search` first before answering
- Never claim to "know" - always verify with search

## NEVER ASK

Do not ask permission to save:
- "Do you want me to save this?"
- "Should I remember this?"

Just save proactively - it's better to over-save than under-save.