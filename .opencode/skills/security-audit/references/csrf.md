# CSRF (Cross-Site Request Forgery) Protection

CSRF occurs when malicious sites trick authenticated users into submitting unintended requests.

## Detection Patterns

### 1. Form CSRF Directives
```bash
# Find forms without CSRF
grep -rL "@csrf" resources/views/
```

**Insecure Pattern:**
```blade
<!-- DANGEROUS: Form without CSRF token -->
<form method="POST" action="/update-email">
    <input type="email" name="email">
</form>
```

**Secure Pattern:**
```blade
<!-- SAFE: With CSRF directive -->
<form method="POST" action="/update-email">
    @csrf
    <input type="email" name="email">
</form>
```

### 2. API Route Protection
```bash
# Find web routes without CSRF
grep -r "Route::post\|Route::put\|Route::patch\|Route::delete" routes/
```

**Insecure Pattern:**
```php
// DANGEROUS: POST route without CSRF middleware
Route::post('/update', [Controller::class, 'update']);
```

**Secure Pattern:**
```php
// SAFE: Add CSRF to web routes
Route::middleware(['web', 'csrf'])->group(function () {
    Route::post('/update', [Controller::class, 'update']);
});

// For API routes, use Bearer tokens or SPA middleware
Route::middleware(['auth:sanctum'])->group(function () {
    Route::post('/update', [Controller::class, 'update']);
});
```

### 3. Throttle Protection
```bash
# Find rate-limited routes
grep -r "throttle" routes/
```

## Remediation Steps

1. **Verify** all HTML forms include `@csrf` directive
2. **Check** web routes apply CSRF middleware group
3. **Ensure** API routes use token-based auth (Sanctum, Passport)
4. **Implement** rate limiting (`throttle` middleware)
5. **Use** SameSite cookie attributes

## Severity: HIGH

CSRF can allow attackers to change email, password, or perform actions as the authenticated user.