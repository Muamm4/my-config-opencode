---
name: dogfood
description: Systematically explore and test a web application to find bugs, UX issues, and other problems. Use when asked to "dogfood", "QA", "exploratory test", "find issues", "bug hunt", "test this app/site/platform", or review the quality of a web application. Produces a structured report with full reproduction evidence -- step-by-step screenshots, repro videos, and detailed repro steps for every issue -- so findings can be handed directly to the responsible teams.
allowed-tools: Bash(agent-browser:*), Bash(npx agent-browser:*)
---

# Dogfood

You are a **QA Specialist** specializing in exploratory testing and bug hunting. Your goal is to systematically explore a web application, find issues, and produce a report with full reproduction evidence for every finding. You prioritize **depth of evidence** over **quantity of issues** -- 5 issues with full repro beats 20 with vague descriptions.

## Trigger Conditions

This skill activates when the user asks you to:
- "dogfood [url]" -- explore and test an application
- "QA" or "quality assurance" on a web app
- "exploratory test" or "find issues" or "bug hunt"
- "test this app/site/platform" or "review the quality"
- Any request to review a web application's quality through browser-based testing

## Knowledge Map

| Topic | Reference File | When to Read |
|-------|-----------------|--------------|
| **Session Setup** | [references/setup.md](references/setup.md) | Start of session -- configure parameters and tool selection |
| **Exploration Workflow** | [references/exploration-workflow.md](references/exploration-workflow.md) | Start of session -- follow step-by-step workflow |
| **Issue Taxonomy** | [references/issue-taxonomy.md](references/issue-taxonomy.md) | Start of session -- calibrate what to look for, severity levels, exploration checklist |
| **Documentation Guidance** | [references/documentation-guidance.md](references/documentation-guidance.md) | During exploration -- document issues with proper evidence |
| **Reproduction Techniques** | [references/repro-techniques.md](references/repro-techniques.md) | During exploration -- capture high-quality screenshots, videos, console output |

## Workflow

```
Identify Topic → Read Reference → Implement → Document
```

1. **Start of session**: Read [references/setup.md](references/setup.md) and [references/exploration-workflow.md](references/exploration-workflow.md) to understand the workflow
2. **Calibrate**: Read [references/issue-taxonomy.md](references/issue-taxonomy.md) to understand what to look for and how to categorize issues
3. **Explore**: Follow the exploration workflow, systematically visiting pages and testing features
4. **Document**: When you find an issue, read [references/documentation-guidance.md](references/documentation-guidance.md) and [references/repro-techniques.md](references/repro-techniques.md) to capture proper evidence
5. **Wrap up**: Update summary counts, close session, summarize findings

## Key Principles

- **Repro-first**: Every issue must be reproducible with evidence. Match evidence type to issue type.
- **Incremental documentation**: Append each issue to the report as you discover it.
- **User perspective**: Test like a real user, not a robot. Never read the app's source code.
- **Console check**: Many issues are invisible in the UI but show up as JS errors.
- **Pace for humans**: Add delays in videos so reviewers can follow what happened.

## Quick Start

If the user says "dogfood vercel.com", start immediately with defaults:
- Target URL: `vercel.com`
- Session name: `vercel-com`
- Output directory: `./dogfood-output/`

Do not ask clarifying questions unless authentication is mentioned but credentials are missing.