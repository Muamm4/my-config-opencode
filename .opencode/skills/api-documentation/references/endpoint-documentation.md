# Endpoint Documentation Guide

This reference covers how to document API endpoints with clear descriptions, parameters, and examples.

## Endpoint Structure

Each endpoint documentation should include:

1. **Summary**: Brief one-line description
2. **Description**: Detailed explanation (optional)
3. **Tags**: Category grouping
4. **OperationId**: Unique identifier
5. **Parameters**: Path, query, header, cookie params
6. **Request Body**: Schema for POST/PUT/PATCH
7. **Responses**: All possible status codes

## Parameter Documentation Table

| Name | Location | Type | Required | Description |
|------|---------|------|----------|-------------|
| `Authorization` | header | string | Yes | Bearer token |
| `user_id` | path | integer | Yes | User ID from URL |
| `page` | query | integer | No | Page number (default: 1) |
| `limit` | query | integer | No | Items per page (default: 15) |

## Example: GET Endpoint

```yaml
/users/{user_id}:
  get:
    summary: Get user by ID
    description: Retrieves a specific user from the system
    tags:
      - Users
    operationId: getUserById
    parameters:
      - name: user_id
        in: path
        required: true
        schema:
          type: integer
        description: The user's unique identifier
      - name: include
        in: query
        schema:
          type: string
        enum: [posts, comments]
        description: Related resources to include
    responses:
      '200':
        description: User found
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/User'
      '404':
        description: User not found
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Error'
```

## Example: POST Endpoint

```yaml
/users:
  post:
    summary: Create new user
    tags:
      - Users
    operationId: createUser
    requestBody:
      required: true
      content:
        application/json:
          schema:
            type: object
            required:
              - email
              - password
              - name
            properties:
              email:
                type: string
                format: email
              password:
                type: string
                format: password
              name:
                type: string
    responses:
      '201':
        description: User created successfully
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/User'
      '422':
        description: Validation error
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Error'
```

## Quick Start Section Template

```markdown
## Quick Start

### Authentication
1. Obtain API key from `/auth/login`
2. Include in header: `Authorization: Bearer <token>`

### List Users
curl -X GET "https://api.example.com/users" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## Best Practices

1. **Always** provide `description` for each parameter
2. **Use** proper `in` values: path, query, header, cookie
3. **Mark** required parameters explicitly
4. **Document** all error responses (not just 200)
5. **Include** a "Quick Start" section for new developers