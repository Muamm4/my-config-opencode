# Tools Usage

Research tools available for stack advisor and how to use them effectively.

## Available Tools

### websearch
- **Purpose**: Current trends, best practices, community consensus
- **Best for**: Verifying if a technology is still active
- **Examples**:
  - "React best practices 2026"
  - "Laravel vs Django performance 2026"
  - "PostgreSQL vs MySQL benchmarks"

### codesearch
- **Purpose**: Real-world implementation examples
- **Best for**: Seeing how technologies are used in production
- **Examples**:
  - "React useState custom hook"
  - "Express middleware authentication"
  - "Laravel Eloquent relationships"

### librarian (via call_omo_agent)
- **Purpose**: Official documentation and patterns
- **Best for**: Accurate, authoritative information
- **Examples**:
  - React documentation
  - Laravel documentation
  - PostgreSQL documentation

### grep_app GitHub Code Search
- **Purpose**: Finding real code patterns
- **Best for**: Understanding actual usage
- **Examples**:
  - Find authentication implementations
  - Find testing patterns
  - Find configuration examples

## Research Workflow

### Before Any Recommendation

1. **Verify Active Status**
   ```
   websearch: "[technology] status 2026" or "[technology] maintainer"
   ```
   - Check last release date
   - Check open issues and responsiveness

2. **Verify Best Practices**
   ```
   websearch: "[technology] best practices"
   codesearch: "[technology] pattern"
   ```
   - Find current recommended approaches
   - Avoid outdated patterns

3. **Verify Compatibility**
   ```
   codesearch: "[tech A] + [tech B] integration"
   ```
   - Check if technologies work well together

## Tool Selection Guide

| Need | Primary Tool | Secondary Tool |
|------|-------------|----------------|
| Current trends | websearch | - |
| Best practices | websearch | codesearch |
| Implementation | codesearch | grep_app |
| Official docs | librarian | - |
| Real-world code | grep_app | codesearch |
| Security status | websearch | - |

## Verification Checklist

Before making any recommendation, verify:

- [ ] Last release date (should be < 6 months ago)
- [ ] Active issue resolution
- [ ] Security policy exists
- [ ] Community size (downloads, stars, contributors)
- [ ] No major unresolved CVEs
- [ ] Compatible with modern tooling