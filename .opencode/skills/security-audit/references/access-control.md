# Broken Access Control (BAC) & IDOR

Broken Access Control occurs when an application fails to properly enforce restrictions on what authenticated users are allowed to do.

## 1. Insecure Direct Object Reference (IDOR)

IDOR is a sub-type of BAC where a user can access any object (record) by simply changing the ID in the request.

### Detection Pattern: The "Ownership Gap"
Look for controllers that fetch a record by ID without verifying that the current user owns that record.

**Insecure Pattern:**
```php
public function show($id)
{
    // DANGEROUS: Fetches any user by ID regardless of who is logged in
    $profile = User::findOrFail($id); 
    return view('profile.show', compact('profile'));
}
```

**Secure Pattern (Ownership Check):**
```php
public function show($id)
{
    // SAFE: Ensures the record belongs to the authenticated user
    $profile = User::where('id', $id)
        ->where('user_id', auth()->id())
        ->firstOrFail();
        
    return view('profile.show', compact('profile'));
}
```

## 2. Privilege Escalation

When a low-privileged user can perform actions reserved for high-privileged users (e.g., Admins).

### Vertical Escalation
- **Pattern**: Admin routes that are not protected by admin-only middleware.
- **Check**: Look for routes like `/admin/settings` or `/admin/users` and verify they use `middleware(['auth', 'admin'])`.

### Horizontal Escalation
- **Pattern**: A user can modify another user's data (e.g., changing another user's password).
- **Check**: Verify that `update` and `delete` methods have ownership checks.

## 3. Auditing Workflow for BAC

1. **Map Privileged Routes**: Identify all routes that should be restricted to specific roles.
2. **Analyze Controller Logic**:
   - Does the method use `findOrFail($id)`? $\to$ **Potential IDOR**.
   - Does the method use `auth()->user()->...` to fetch related data? $\to$ **Likely Secure**.
3. **Check Middleware**:
   - Are the routes grouped under the correct middleware?
   - Is there a custom `Gate` or `Policy` being used? (`$this->authorize('update', $post)`)
4. **Test Scenarios**:
   - "Can I access `/api/orders/100` if I am logged in as User B but Order 100 belongs to User A?"
   - "Can I call the `delete` method on a resource I don't own?"

## Severity: CRITICAL
BAC/IDOR often leads to massive data breaches and unauthorized administrative access.
