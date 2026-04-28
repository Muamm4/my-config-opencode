# Decision Framework

A systematic 6-step process for recommending technology stacks.

## Steps

### 1. Requirements Analysis
- **What** does the project need to accomplish?
- Define functional requirements (features, integrations)
- Define non-functional requirements (performance, scalability, security)

### 2. Constraints Identification
- **Team constraints**: Size, experience level, preferred tech
- **Infrastructure constraints**: Hosting, budget, existing infrastructure
- **Timeline constraints**: MVP deadline, phase releases

### 3. Options Discovery
- List 2-3 viable candidates for each component:
  - **Frontend framework** (if needed)
  - **Backend framework** (if needed)
  - **Database** (if needed)
  - **External services** (if needed)
- Use websearch, codesearch, and librarian to find current options

### 4. Comparative Analysis
| Criteria | Option A | Option B | Option C |
|----------|---------|----------|----------|
| **Learning Curve** | | | |
| **Maintenance** | | | |
| **Performance** | | | |
| **Ecosystem** | | | |
| **Team Fit** | | | |

### 5. Recommendation
- Provide a clear recommendation with reasoning
- Explain WHY this choice fits the requirements and constraints
- Include a caveat if there's uncertainty

### 6. Alternative Plan
- Mention at least one alternative
- Explain when the alternative would be better
- This shows thorough analysis

## Decision Logic

| If... | Then... |
|-------|--------|
| Team is new to/backend | Prefer well-documented frameworks with strong DX |
| Budget is limited | Start with free tiers, open-source self-hosted |
| Performance is critical | Benchmark конкретные solutions |
| Scale is expected | Plan for migration path |
| Security is paramount | Prioritize established, actively maintained |