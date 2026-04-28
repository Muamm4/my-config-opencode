# Expect API

Pest's expectation API using `expect()`.

## Basic Assertions

```php
expect($user->name)->toBe('John');
expect($user->email)->toContain('@');
expect($response->status())->toBe(200);
expect($response->json('data'))->toHaveKey('id');
```

## Matchers

| Matcher | Usage |
|--------|-------|
| `toBe($value)` | Exact equality |
| `toEqual($value)` | Deep equality |
| `toContain($value)` | String/array contains |
| `toBeNull()` | Is null |
| `toBeTrue()` / `toBeFalse()` | Boolean check |
| `toBeGreaterThan($n)` | Numeric comparison |
| `toBeLessThan($n)` | Numeric comparison |
| `toHaveKey($key)` | Array has key |
| `toBeInstanceOf($class)` | Instance check |
| `toThrow($exception)` | Exception thrown |

## Exception Testing

```php
expect(fn () => $this->delete('/invalid'))->toThrow(Exception::class);
expect(fn () => $this->delete('/invalid'))->toThrow(Exception::class, 'Error message');
```

## Array/Collection Expectations

```php
expect($users)->toHaveCount(5);
expect($users[0]->name)->toBe('John');
expect($posts->first())->toBeInstanceOf(Post::class);
```