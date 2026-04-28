# JSON:API Standards Integration

This reference covers how to ensure strict consistency with JSON:API specification when documenting APIs.

## JSON:API Response Format

### Successful Response

```json
{
  "data": {
    "id": "1",
    "type": "users",
    "attributes": {
      "name": "John Doe",
      "email": "john@example.com"
    },
    "relationships": {
      "posts": {
        "links": {
          "self": "/users/1/relationships/posts",
          "related": "/users/1/posts"
        }
      }
    },
    "links": {
      "self": "/users/1"
    }
  },
  "included": [
    {
      "id": "5",
      "type": "posts",
      "attributes": {
        "title": "First Post"
      }
    }
  ],
  "meta": {
    "apiVersion": "1.0.0"
  }
}
```

### Error Response

```json
{
  "errors": [
    {
      "status": "422",
      "title": "Validation Failed",
      "detail": "The email field is required",
      "source": {
        "pointer": "/data/attributes/email"
      }
    }
  ],
  "meta": {
    "traceId": "abc-123"
  }
}
```

## JSON:API Conventions

| Convention | Standard |
|-----------|----------|
| Root object | Must be object (not array) |
| Resource identifier | `{ id, type }` |
| Attributes | `attributes` key (no relationships) |
| Relationships | `relationships` key |
| Errors | `errors` array at root |

## Documenting JSON:API Endpoints

When documenting, ensure your schema follows JSON:API format:

```yaml
components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: string
          description: Resource ID
        type:
          type: string
          enum: [users]
        attributes:
          $ref: '#/components/schemas/UserAttributes'
        relationships:
          $ref: '#/components/schemas/UserRelationships'
    UserAttributes:
      type: object
      properties:
        name:
          type: string
        email:
          type: string
          format: email
        createdAt:
          type: string
          format: date-time
```

## Best Practices

1. **Always** wrap single resources in `{ data: {...} }`
2. **Use** `{ data: [...] }` for collections
3. **Never** include relationships in attributes object
4. **Always** include `type` in every resource
5. **Use** ISO 8601 for dates (`format: date-time`)