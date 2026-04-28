# Permissions & Roles (Spatie)

## Setup

### Install Package
```bash
composer require spatie/laravel-permission
```

### Publish Config
```bash
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
```

### Run Migration
```bash
php artisan migrate
```

## Usage

### Assign Role to User
```php
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;

// Assign single role
$user->assignRole('admin');

// Assign multiple roles
$user->assignRole(['editor', 'author']);

// Remove role
$user->removeRole('editor');
```

### Check Permissions
```php
// Check single permission
$user->can('edit users');

// Check multiple permissions
$user->can(['edit users', 'delete users']);

// Check role
$user->hasRole('admin');
```

### Create Roles & Permissions
```php
// Create role
$role = Role::create(['name' => 'editor']);
$role->givePermissionTo('edit posts');

// Create permission
$permission = Permission::create(['name' => 'edit posts']);
$role->givePermissionTo($permission);
```

### Middleware
```php
// In routes/web.php
Route::middleware(['auth', 'role:admin'])->group(function () {
    // Admin only routes
});

// Or permission middleware
Route::middleware(['auth', 'permission:edit users'])->group(function () {
    // Routes requiring edit users permission
});
```

### Blade Directives
```blade
@role('admin')
    <p>Admin content</p>
@endrole

@can('edit users')
    <button>Edit</button>
@endcan
```

## Models

```php
// User model should use HasRoles trait
use Spatie\Permission\Traits\HasRoles;

class User extends Model
{
    use HasRoles;
}
```