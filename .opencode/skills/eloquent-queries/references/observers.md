# Observers

Pattern reference for Eloquent model observers in Laravel.

## Creating an Observer

```php
// app/Observers/UserObserver.php
<?php

namespace App\Observers;

use App\Models\User;

class UserObserver
{
    // Called before creating
    public function creating(User $user)
    {
        // Generate UUID
        $user->uuid = Str::uuid();
        
        // Set default role
        if (empty($user->role)) {
            $user->role = 'user';
        }
    }

    // Called after creating
    public function created(User $user)
    {
        // Send welcome email
        Mail::to($user->email)->send(new WelcomeMail($user));
        
        // Log activity
        activity()->log('User registered');
    }

    // Called before updating
    public function updating(User $user)
    {
        // Track changes
        if ($user->isDirty('email')) {
            $user->email_verified_at = null;
        }
    }

    // Called after updating
    public function updated(User $user)
    {
        if ($user->wasChanged('status')) {
            // Notify status change
        }
    }

    // Called before saving (create or update)
    public function saving(User $user)
    {
        // Normalize data
        $user->email = strtolower($user->email);
    }

    // Called after saving
    public function saved(User $user)
    {
        // Clear cache
        Cache::forget("user:{$user->id}");
    }

    // Called before deleting
    public function deleting(User $user)
    {
        // Soft delete related
        $user->posts()->delete();
    }

    // Called after deleting
    public function deleted(User $user)
    {
        // Log deletion
        activity()->log("User {$user->id} deleted");
    }

    // Called when force deleting
    public function forceDeleting(User $user)
    {
        // Permanently delete related
        $user->posts()->forceDelete();
    }

    // Called after restore
    public function restored(User $user)
    {
        // Restore related
        $user->posts()->restore();
    }
}
```

## Registering Observer

```php
// Option 1: In AppServiceProvider boot
use App\Models\User;
use App\Observers\UserObserver;

public function boot()
{
    User::observe(UserObserver::class);
}

// Option 2: In Model (boot method)
class User extends Model
{
    protected static function booted()
    {
        static::observe(UserObserver::class);
    }
}
```

## Observer Events Order

```
creating → created
updating → updated
saving → saved
deleting → deleted
restoring → restored
forceDeleting → forceDeleted
```

## Observing Multiple Models

```php
class AuditObserver
{
    public function created(Model $model)
    {
        AuditLog::create([
            'model' => get_class($model),
            'model_id' => $model->id,
            'action' => 'created',
            'data' => $model->toArray()
        ]);
    }

    public function updated(Model $model)
    {
        if ($model->isDirty()) {
            AuditLog::create([
                'model' => get_class($model),
                'model_id' => $model->id,
                'action' => 'updated',
                'changes' => $model->getChanges()
            ]);
        }
    }

    public function deleted(Model $model)
    {
        AuditLog::create([
            'model' => get_class($model),
            'model_id' => $model->id,
            'action' => 'deleted'
        ]);
    }
}

// Register for multiple models
User::observe(AuditObserver::class);
Post::observe(AuditObserver::class);
Order::observe(AuditObserver::class);
```

## Observers vs Events

```php
// Observer approach (recommended)
class UserObserver
{
    public function created(User $user)
    {
        // Handle
    }
}

// Event approach (alternative)
class User extends Model
{
    protected $dispatchesEvents = [
        'created' => UserCreated::class,
        'updated' => UserUpdated::class,
    ];
}

// Event class
class UserCreated
{
    use Dispatchable, InteractsWithQueue, SerializesModels;

    public $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }
}
```