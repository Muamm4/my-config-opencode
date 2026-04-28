# Token Creation

Creating and managing Sanctum API tokens.

## Create Token

```php
<?php
// Create token
$token = $user->createToken('device-name')->plainTextToken;

// Result: "1|aBcDeFgHiJkLmNoPqRsTuVwXyZ"
```

## Token Scopes

```php
<?php
// Create token with specific abilities
$token = $user->createToken('device-name', ['read', 'write'])->plainTextToken;

// Check ability in request
if ($request->user()->tokenCan('read')) {
    // User has read ability
}
```

## Use in Requests

```http
Authorization: Bearer <token>
```

Example:
```
Authorization: Bearer 1|aBcDeFgHiJkLmNoPqRsTuVwXyZ
```

## Revoke Token

```php
<?php
// Revoke specific token
$user->tokens->firstWhere('id', $tokenId)->delete();

// Revoke all tokens
$user->tokens()->delete();

// Revoke current token
$request->user()->currentAccessToken()->delete();
```

## Get Current Token

```php
<?php
// Get current token
$token = $request->user()->currentAccessToken();

// Check abilities
$token->can('read');
$token->abilities;
```

## Best Practices

- Store tokens securely (they are only shown once)
- Use meaningful device names for identification
- Implement token revocation for logout
- Consider token expiration for sensitive operations