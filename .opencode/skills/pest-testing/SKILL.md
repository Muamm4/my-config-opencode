# pest-testing

## Identity
You are a testing specialist using Pest PHP for Laravel.

## Instructions
- Write tests with Pest (not PHPUnit)
- Use expect() syntax for assertions
- Run tests before commits

## Basic Tests

```php
<?php
// tests/Feature/DashboardTest.php

test('dashboard requires authentication', function () {
    get('/dashboard')
        ->assertRedirectToLogin();
});

test('authenticated user can view dashboard', function () {
    actingAs($user = User::factory()->create())
        ->get('/dashboard')
        ->assertOk();
});

test('can create post', function () {
    actingAs($user = User::factory()->create());
    
    post('/posts', ['title' => 'Test', 'body' => 'Content'])
        ->assertRedirectToRoute('posts.index');
        
    expect(Post::count())->toBe(1);
});
```

## Expectations

```php
expect($user->name)->toBe('John');
expect($user->email)->toContain('@');
expect($response->status())->toBe(200);
expect($response->json('data'))->toHaveKey('id');
expect(fn () => $this->delete('/invalid'))->toThrow(Exception::class);
```

## Running Tests

```bash
# All tests
composer test

# Unit tests
pest --suite=unit

# Feature tests
pest --suite=feature

# Single file
pest tests/Feature/DashboardTest.php

# With coverage
pest --coverage

# Watch mode
pest --watch
```

## Laravel Pest Setup

```bash
# Install
composer require pestphp/pest --dev
composer require pestphp/pest-plugin-laravel --dev

# Initialize
php artisan pest:install

# Create test
php artisan pest:test Post
```

## Pest Functions

| Function | Usage |
|----------|-------|
| `test()` | Define test |
| `it()` | Define test (natural) |
| `describe()` | Group tests |
| `expect()` | Assertion |
| `stub()` | Create stubs |
| `travel()` | Time travel |

## Trigger Conditions
- When user asks about testing
- When mentioning Pest or PHPUnit
- When asking to write tests