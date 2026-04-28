# Request Examples Guide

This reference provides copy-pasteable request examples in curl and JavaScript/fetch.

## curl Examples

### GET Request

```bash
# Basic GET
curl -X GET "https://api.example.com/users" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Accept: application/vnd.api+json"

# With query parameters
curl -X GET "https://api.example.com/users?page=1&limit=15" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Accept: application/vnd.api+json"
```

### POST Request

```bash
# Create resource
curl -X POST "https://api.example.com/users" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/vnd.api+json" \
  -H "Accept: application/vnd.api+json" \
  -d '{
    "data": {
      "type": "users",
      "attributes": {
        "name": "John Doe",
        "email": "john@example.com",
        "password": "secret123"
      }
    }
  }'
```

### PUT Request

```bash
# Update resource
curl -X PUT "https://api.example.com/users/1" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -H "Content-Type: application/vnd.api+json" \
  -H "Accept: application/vnd.api+json" \
  -d '{
    "data": {
      "id": "1",
      "type": "users",
      "attributes": {
        "name": "John Updated"
      }
    }
  }'
```

### DELETE Request

```bash
# Delete resource
curl -X DELETE "https://api.example.com/users/1" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

## JavaScript Examples

### GET Request

```javascript
// Using fetch
const response = await fetch('https://api.example.com/users', {
  method: 'GET',
  headers: {
    'Authorization': 'Bearer YOUR_TOKEN',
    'Accept': 'application/vnd.api+json'
  }
});

const data = await response.json();
```

### POST Request

```javascript
// Create resource
const response = await fetch('https://api.example.com/users', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer YOUR_TOKEN',
    'Content-Type': 'application/vnd.api+json',
    'Accept': 'application/vnd.api+json'
  },
  body: JSON.stringify({
    data: {
      type: 'users',
      attributes: {
        name: 'John Doe',
        email: 'john@example.com',
        password: 'secret123'
      }
    }
  })
});

const data = await response.json();
```

### Axios Example

```javascript
import axios from 'axios';

const client = axios.create({
  baseURL: 'https://api.example.com',
  headers: {
    'Accept': 'application/vnd.api+json',
    'Content-Type': 'application/vnd.api+json'
  }
});

// Add auth interceptor
client.interceptors.request.use(config => {
  config.headers.Authorization = `Bearer ${getToken()}`;
  return config;
});

// GET
const { data } = await client.get('/users');

// POST
const { data } = await client.post('/users', {
  data: {
    type: 'users',
    attributes: { name: 'John' }
  }
});
```

## Best Practices

1. **Always** include `Accept` header for JSON:API
2. **Always** include `Content-Type` for POST/PUT
3. **Always** include `Authorization` header
4. **Use** `-H` for each header (not combined)
5. **Format** JSON with proper indentation