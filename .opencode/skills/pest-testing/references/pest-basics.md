# Pest PHP Basics

Core testing patterns using Pest PHP framework.

## Test Definition

```php
test('test name', function () {
    // test code
});

it('can describe test in natural language', function () {
    // test code
});

// Group tests with describe()
describe('Post Model', function () {
    it('can be created', function () {
        // test code
    });
    
    it('has a title', function () {
        // test code
    });
});
```

## Laravel HTTP Tests

```php
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

## Common Patterns

- Use `actingAs()` to simulate authenticated users
- Use HTTP verbs: `get()`, `post()`, `put()`, `patch()`, `delete()`
- Chain assertion methods: `->assertOk()`, `->assertRedirectToRoute()`, etc.
- Use `User::factory()->create()` for test data