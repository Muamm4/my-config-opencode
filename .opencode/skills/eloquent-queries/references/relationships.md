# Relationships

Eloquent relationship patterns for Laravel models.

## One to One

```php
// In User model
class User extends Model
{
    public function profile(): HasOne
    {
        return $this->hasOne(Profile::class);
    }

    // With custom foreign key
    public function profile(): HasOne
    {
        return $this->hasOne(Profile::class, 'user_id');
    }

    // Inverse (in Profile model)
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}

// Access
$user = User::find(1);
$profile = $user->profile;
```

## One to Many

```php
// In User model
class User extends Model
{
    public function posts(): HasMany
    {
        return $this->hasMany(Post::class);
    }

    // With custom key
    public function posts(): HasMany
    {
        return $this->hasMany(Post::class, 'author_id');
    }
}

// Inverse (in Post model)
class Post extends Model
{
    public function author(): BelongsTo
    {
        return $this->belongsTo(User::class, 'author_id');
    }
}

// Access
$posts = User::find(1)->posts;
```

## Many to Many

```php
// In User model
class User extends Model
{
    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class);
    }

    // With pivot table
    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class, 'user_roles');
    }

    // With pivot columns
    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class)
            ->withPivot('created_at', 'expires_at')
            ->withTimestamps();
    }
}

// Access
$roles = User::find(1)->roles;

// Attach/Detach/Sync
$user->roles()->attach($roleId);
$user->roles()->detach($roleId);
$user->roles()->sync([1, 2, 3]);
$user->roles()->syncWithoutDetaching([4]);
```

## Has Many Through

```php
// Country has many Posts through Users
class Country extends Model
{
    public function posts(): HasManyThrough
    {
        return $this->hasManyThrough(Post::class, User::class);
    }

    // With custom keys
    public function posts(): HasManyThrough
    {
        return $this->hasManyThrough(
            Post::class,
            User::class,
            'country_id',  // Foreign key on users table
            'author_id',   // Foreign key on posts table
            'id'           // Local key on countries table
        );
    }
}
```

## Polymorphic

```php
// MorphTo (in Comment model)
class Comment extends Model
{
    public function commentable(): MorphTo
    {
        return $this->morphTo();
    }
}

// MorphMany (in Post model)
class Post extends Model
{
    public function comments(): MorphMany
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}

// MorphOne (in Image model)
class Image extends Model
{
    public function imageable(): MorphTo
    {
        return $this->morphTo();
    }
}

// Usage
$post->comments()->create(['body' => 'Great post!']);
$image = $post->image; // Returns Image model
```

## MorphToMany

```php
// Tagging system
class Post extends Model
{
    public function tags(): MorphToMany
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}

class Video extends Model
{
    public function tags(): MorphToMany
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}

// Usage
$post->tags()->attach($tagId);
$video->tags()->attach($tagId); // Same tag can be on both
```

## Querying Relationships

```php
// With relationship constraint
$users = User::whereHas('posts', function ($q) {
    $q->where('published', true);
})->get();

// WithCount (adds virtual column)
$users = User::withCount('posts')->get();
// $user->posts_count

// WithCount with condition
$users = User::withCount(['posts' => function ($query) {
    $query->where('published', true);
}])->get();

// Has (minimum count)
$users = User::has('posts', '>=', 5)->get();

// DoesntHave
$users = User::doesntHave('profile')->get();