# The Discovery Stub Architecture

The Discovery Stub is the gold standard for complex AI skills. Instead of a single, massive `SKILL.md` that consumes excessive context, this architecture separates the **Entry Point** from the **Knowledge Base**.

## Structure
```
skill-name/
├── SKILL.md              # The Discovery Stub (Entry Point)
└── references/           # The Knowledge Base (Detailed Docs)
    ├── topic-a.md
    ├── topic-b.md
    └── ...
```

## 1. The Discovery Stub (SKILL.md)
The main file acts as a router. It should contain:
- **Identity**: A clear, concise role.
- **Version/Requirements**: Any technical constraints.
- **Knowledge Map**: A table mapping topics to reference files.
- **Workflow**: Instructions on how the agent should use the references (e.g., "Identify topic $\rightarrow$ Read reference $\rightarrow$ Implement").
- **Trigger Conditions**: When this skill should be activated.

## 2. The Reference Files (`references/*.md`)
These files contain the "heavy" information. They should be:
- **Atomic**: One file per major topic.
- **Pattern-Oriented**: Focus on "How to do X" with concrete code examples.
- **Exhaustive**: Include edge cases, common pitfalls, and best practices.

## Why use this pattern?
1. **Context Efficiency**: The agent only reads the specific reference it needs, saving thousands of tokens.
2. **Maintainability**: Updating a specific pattern doesn't require rewriting the entire skill.
3. **Scalability**: You can add dozens of reference files without degrading the agent's performance.
4. **Precision**: Reduces "hallucinations" by forcing the agent to look up the actual pattern before implementing.
