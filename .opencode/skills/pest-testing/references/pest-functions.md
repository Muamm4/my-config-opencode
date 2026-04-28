# Pest Functions

Reference guide for Pest utility functions.

## Core Functions

| Function | Usage |
|---------|-------|
| `test()` | Define a test |
| `it()` | Define test (natural language) |
| `describe()` | Group related tests |
| `expect()` | Create expectation |
| `stub()` | Create test doubles |
| `patch()` | Mock objects |

## Testing Utilities

| Function | Usage |
|---------|-------|
| `actingAs($user)` | Simulate authenticated user |
| `travel()` | Time travel in tests |
| `fake()` | Fake email/mail fake |
| `refreshDatabase()` | Reset DB for each test |
| `actingAsGuest()` | Simulate unauthenticated user |

## Time Travel

```php
// Travel to future
travel()->to('+1 day');
expect(Carbon::now()->toDateString())->toBe(today()->addDay()->toDateString());

// Travel to past
travel()->back();

// Reset to present
travel()->toNow();
```

## Faker

```php
fake()->name();       // Random name
fake()->email();      // Random email
fake()->sentence();   // Random sentence
fake()->paragraph(); // Random paragraph
```

## Available Matches

Check Pest docs for full list of matchers:
- `toBe()`, `toEqual()`, `toContain()`
- `toBeNull()`, `toBeTrue()`, `toBeFalse()`
- `toBeGreaterThan()`, `toBeLessThan()`
- `toHaveKey()`, `toHaveCount()`
- `toBeInstanceOf()`, `toThrow()`