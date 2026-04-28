# Laravel Migrations for PostgreSQL

## Basic Migration

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamp('email_verified_at')->nullable();
            $table->string('password');
            $table->rememberToken();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('users');
    }
};
```

## PostgreSQL-Specific Types

### UUID

```php
$table->uuid('uuid')->unique();

// Or with automatic UUID generation
$table->uuid('id')->primary();
// Then in model:
// protected $keyType = 'string';
// public $incrementing = false;
```

### JSON/JSONB

```php
$table->json('data')->nullable();        // JSON (text)
$table->jsonb('metadata')->nullable(); // JSONB (binary, faster)
// JSONB operations in PostgreSQL:
// SELECT * FROM table WHERE data->>'key' = 'value';
// SELECT * FROM table WHERE data @> '{"key": "value"}';
```

### Array

```php
// PostgreSQL array type (requires doctrine/dbal)
use Doctrine\DBAL\Types\Type;

$table->json('tags')->nullable(); // Store as JSON, query with PostgreSQL functions

// Raw SQL for true array:
DB::statement('ALTER TABLE posts ADD COLUMN tags text[]');
```

### Hstore (Key-Value)

```php
// Enable extension first
DB::statement('CREATE EXTENSION IF NOT EXISTS hstore');

$table->addColumn('hstore', 'metadata')->nullable();
```

### Point/Geography (Spatial)

```php
// Enable postgis
DB::statement('CREATE EXTENSION IF NOT EXISTS postgis');

$table->geography('location', 4326)->nullable();
$table->point('location')->nullable();
```

## PostgreSQL Constraints

### Unique Constraint with Condition

```php
$table->unique(['email', 'tenant_id']); // Composite unique

// Unique constraint withPartial unique index
DB::statement('CREATE UNIQUE INDEX active_users_idx 
    ON users (email) 
    WHERE status = \'active\'');
```

### Exclusion Constraint

```php
// Prevent overlapping date ranges
DB::statement('ALTER TABLE bookings ADD EXCLUDE USING gist (
    tsrange WITH &&,
    id WITH =
)');
```

### Deferrable Constraints

```php
DB::statement('ALTER TABLE order_items 
    ADD CONSTRAINT order_items_order_id_fk 
    FOREIGN KEY (order_id) REFERENCES orders(id)
    DEFERRABLE INITIALLY DEFERRED');
```

## Index Types

### Index with WHERE

```php
$table->index('email')->where('active', true);

// Or raw
DB::statement('CREATE INDEX active_users_email_idx 
    ON users (email) 
    WHERE active = true');
```

### Partial Index

```php
// Index only active users
DB::statement('CREATE INDEX users_active_idx 
    ON users (created_at) 
    WHERE status = \'active\'');
```

### Composite Index

```php
$table->index(['tenant_id', 'status', 'created_at']);
```

### GIN/GiST Indexes

```php
// For JSONB
DB::statement('CREATE INDEX idx_gin ON posts USING gin (data ginbpath)");

// For full-text search
DB::statement('CREATE INDEX idx_fts ON posts USING gin (to_tsvector(\'english\', title))');
```

## Migrations with Seeds

```php
// generate-uuid.php in database/migrations
use Illuminate\Support\Str;

class CreateUuid extends Migration
{
    public function up(): void
    {
        Schema::create('uuid_test', function (Blueprint $table) {
            $table->uuid('id')->primary();
            $table->string('name');
            $table->timestamps();
        });
    }
}

// In seeder:
DB::table('uuid_test')->insert([
    'id' => Str::uuid(),
    'name' => 'Test',
    'created_at' => now(),
    'updated_at' => now(),
]);
```

## Common Patterns

### Soft Deletes

```php
$table->softDeletes();

// Uses: deleted_at NULL = not deleted
// Query: User::withTrashed()->where('id', $id)->restore();
```

### BigInteger Primary Key

```php
$table->bigIncrements('id');  // bigint unsigned (not bigserial)
$table->bigInteger('id');    // Use for PostgreSQL bigserial compatibility
```

### Using Schema Builder with Raw

```php
// For complex PostgreSQL features
DB::statement('CREATE TABLE ...');

// For migrations that need PostgreSQL-specific syntax
Schema::create('events', function (Blueprint $table) {
    $table->id();
    $table->string('name');
});
```

## Important Notes

- Use `bigIncrements()` for PostgreSQL (not `increments()`)
- `prefix_indexes = true` in config/database.php
- Always specify schema: `schema => 'public'`
- Use `jsonb` for better query performance