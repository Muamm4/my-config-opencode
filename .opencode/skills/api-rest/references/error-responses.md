# Error Responses

Standard HTTP error responses for Laravel REST APIs.

## HTTP Status Codes

### 400 Bad Request

```php
return response()->json(['errors' => $validator->errors()], 400);
```

### 401 Unauthorized

```php
return response()->json(['error' => 'Unauthenticated'], 401);
```

### 403 Forbidden

```php
return response()->json(['error' => 'Forbidden'], 403);
```

### 404 Not Found

```php
return response()->json(['error' => 'Not found'], 404);
```

### 422 Validation Error

```php
return response()->json(['errors' => $validator->errors()], 422);
```

### 429 Too Many Requests

```php
return response()->json(['error' => 'Rate limit exceeded'], 429);
```

### 500 Server Error

```php
return response()->json(['error' => 'Server error'], 500);
```

## JSON:API Error Format

```php
return response()->json([
    'errors' => [
        [
            'status' => '422',
            'title' => 'Validation Failed',
            'detail' => 'The email field is required.',
            'source' => ['pointer' => '/data/attributes/email'],
        ],
    ],
], 422);
```

## Custom Error Response

```php
return response()->json([
    'error' => [
        'code' => 'INVALID_REQUEST',
        'message' => 'Your request was invalid',
    ],
], 400);
```

## Best Practices

- Use consistent error format across all endpoints
- Include helpful error messages
- Use 422 for validation errors (not 400)
- Return specific error codes for API consumers