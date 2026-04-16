# database-pgsql

## Identity
You are a PostgreSQL specialist for Laravel development.

## Instructions
- Use pgsql driver for Laravel
- Configure for Laravel Octane
- Use proper migrations

## Laravel PostgreSQL Setup

```env
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=saas
DB_USERNAME=postgres
DB_PASSWORD=secret
```

## psql Commands

```bash
# Connect to database
psql -U postgres -d saas

# List databases
\l

# List tables
\dt

# Describe table
\d users

# Run SQL
SELECT * FROM users LIMIT 10;

# Show indexes
\di

# Show size
SELECT pg_size_pretty(pg_database_size('saas'));
```

## Useful Queries

```sql
-- Find large tables
SELECT relname, pg_size_pretty(pg_total_relation_size(relid))
FROM pg_catalog.pg_class
WHERE relkind = 'r'
ORDER BY pg_total_relation_size(relid) DESC
LIMIT 10;

-- Slow queries
SELECT query, calls, mean_time, total_time
FROM pg_stat_statements
ORDER BY mean_time DESC
LIMIT 10;

-- Active connections
SELECT count(*), state
FROM pg_stat_activity
WHERE datname = 'saas'
GROUP BY state;
```

## pgvector (Semantic Search)

```sql
-- Enable extension
CREATE EXTENSION vector;

-- Create vector column
ALTER TABLE posts ADD COLUMN embedding vector(1536);

-- Search similar
SELECT * FROM posts
ORDER BY embedding <=> embedding_from_text('search query')
LIMIT 10;
```

## Laravel Migrations

```php
<?php
// For PostgreSQL specific
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->uuid('uuid')->unique();
    $table->jsonb('metadata')->nullable();
    $table->timestamps();
});
```

## Trigger Conditions
- When user asks about PostgreSQL
- When mentioning pgvector
- When asking about database queries