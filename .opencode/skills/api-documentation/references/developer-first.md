# Developer-First Documentation Principles

This reference covers the "Developer-First" approach for API documentation, prioritizing clarity, brevity, and usability.

## Core Principles

### 1. Clarity Over Completeness
- Write concise descriptions that answer "What does this endpoint do?"
- Avoid jargon and acronyms without explanation
- Use active voice: "Creates a user" not "User creation is performed"

### 2. Brevity Within Accuracy
- Keep descriptions under 2 sentences when possible
- Save deep details for the "description" field, not summary
- Use Tables for parameters (scannable)

### 3. Usability First
- Every endpoint needs working examples
- Include "Quick Start" section for new developers
- Test your documentation works (copy-paste examples)

## Documentation Quality Checklist

| Element | Requirement |
|---------|-------------|
| Summary | One line, clear purpose |
| Description | What it does, not how internally |
| Parameters | All params documented with table |
| Response | At least 200 and error codes |
| Example | Working curl/request snippet |

## Quick Start Section Template

For new developers integrating with your API:

```markdown
## Quick Start

### 1. Get Authentication Token
```bash
curl -X POST "https://api.example.com/auth/login" \
  -H "Content-Type: application/json" \
  -d '{"email": "dev@example.com", "password": "secret"}'
```

### 2. List Resources
```bash
curl -X GET "https://api.example.com/users" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### 3. Create Resource
```bash
curl -X POST "https://api.example.com/users" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"name": "New User"}'
```
```

## Parameter Table Format

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `id` | integer | Yes | Unique user identifier |
| `name` | string | No | Display name (max 255 chars) |
| `email` | string | Yes | Valid email address |
| `role` | enum | No | `admin`, `user` (default: `user`) |

## Best Practices

1. **Prioritize** the happy path in examples
2. **Show** error responses for common failures
3. **Use** consistent naming across all endpoints
4. **Group** related endpoints under same tag
5. **Include** version in URL if applicable