# OpenAPI/Swagger Standards

This reference covers the fundamental OpenAPI 3.0 specification patterns for API documentation.

## OpenAPI Document Structure

```yaml
openapi: 3.0.3
info:
  title: API Name
  version: 1.0.0
  description: API description
servers:
  - url: https://api.example.com
    description: Production server
paths:
  /users:
    get:
      summary: List all users
      tags:
        - Users
      responses:
        '200':
          description: Successful response
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'
components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
          example: 1
        name:
          type: string
          example: John Doe
```

## Key Components

| Component | Purpose |
|-----------|---------|
| `info` | API metadata (title, version, description) |
| `servers` | Base URLs for API endpoints |
| `paths` | All available endpoints |
| `components/schemas` | Reusable schema definitions |
| `components/securitySchemes` | Authentication methods |

## Schema Types

- `string`, `integer`, `number`, `boolean`, `array`, `object`
- Use `enum` for fixed values
- Use `nullable: true` for optional nulls
- Use `required: []` array for mandatory fields

## Common Patterns

### Object Schema
```yaml
User:
  type: object
  required:
    - email
    - password
  properties:
    id:
      type: integer
      readOnly: true
    email:
      type: string
      format: email
    name:
      type: string
      nullable: true
```

### Array Response
```yaml
UsersListResponse:
  type: object
  properties:
    data:
      type: array
      items:
        $ref: '#/components/schemas/User'
    meta:
      $ref: '#/components/schemas/Pagination'
```

### Error Response
```yaml
Error:
  type: object
  properties:
    message:
      type: string
    errors:
      type: array
      items:
        type: object
        properties:
          field:
            type: string
          message:
            type: string
```

## Best Practices

1. **Always** define reusable schemas in `components/schemas`
2. **Use** `readOnly: true` for fields that are output-only
3. **Include** `example` values for all schema properties
4. **Group** endpoints by tag for organized documentation
5. **Document** all possible response codes (200, 400, 401, 403, 404, 500)