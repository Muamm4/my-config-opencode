# Domain Guidelines

Domain-specific guidance for technology recommendations.

## Web Frontend

### Default Recommendations
- **Preferred**: Vanilla JavaScript + CSS
- **CSS**: Plain CSS or CSS modules (avoid CSS-in-JS unless needed)
- **Build**: Vite (fast, simple, extensible)

### When to Recommend Frameworks
| Scenario | Recommendation |
|----------|-------------|
| Complex state management needed | React or Vue |
| Server-rendered content (SEO) | Next.js, Nuxt |
| Rich interactive features | React |
| Rapid prototyping | Vue or Svelte |
| Static site | Astro |

### Principles
- Prefer simple over complex
- Avoid heavy frameworks unless requirements justify them
- Consider bundle size impact
- Think about render performance

## Backend

### Default Recommendations
- **Language**: Match team's strongest expertise
- **Framework**: Well-established (Laravel, Django, Express, Rails)
- **API**: REST or GraphQL based on frontend needs

### When to Recommend Different Frameworks
| Scenario | Recommendation |
|----------|-------------|
| Team expertise in PHP | Laravel |
| Team expertise in Python | Django, FastAPI |
| Team expertise in Node.js | Express, NestJS |
| Microservices needed | Go, Node.js |
| Real-time features needed | Node.js, Laravel (Reverb) |

### Principles
- Team experience FIRST
- Good DX matters for maintenance
- Consider hosting ecosystem

## Databases

### Starting Points
- **SQLite**: Prototypes, small apps, single-user
- **PostgreSQL**: Production apps, relational needs
- **MongoDB**: Document-heavy, flexible schemas

### Scaling Guidelines
| Scale | Recommendation |
|-------|----------------|
| < 10K rows/day | SQLite |
| < 1M rows/day | PostgreSQL |
| > 1M rows/day | PostgreSQL + Read Replicas |
| Geo-distributed | CockroachDB, PlanetScale |

### When to Consider Alternatives
- **Redis**: Caching, sessions, real-time
- **Elasticsearch**: Full-text search
- **S3**: File storage
- **ClickHouse**: Analytics workloads

### Principles
- Start simple, scale when needed
- PostgreSQL handles most use cases well
- Don't pre-optimize without evidence

## AI & Code Agents

### Default Recommendations
- **Preferred**: API-based solutions
- **Examples**: OpenAI API, Anthropic, Gemini

### When to Recommend Self-Hosted
| Scenario | Recommendation |
|----------|-------------|
| Data privacy (HIPAA, GDPR) | Self-hosted + private endpoint |
| Custom fine-tuned models | Self-hosted |
| Cost at massive scale | Self-hosted (but expensive to maintain) |
| Offline requirement | Self-hosted |

### Principles
- **Default to APIs** over self-hosted
- Self-hosted has hidden costs (maintenance, infrastructure)
- API providers improve faster than self-hosted
- Security updates matter more than features

## Security Considerations

### Always Prioritize
- **Maintenance**: Actively maintained > feature-rich
- **Security**: CVE response time matters
- **Community**: Size of ecosystem, number of contributors
- **Documentation**: Quality and completeness

### Red Flags
- No releases in > 6 months
- Unresolved security issues
- Abandoned repositories
- No security policy defined