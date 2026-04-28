# PostgreSQL Indexing Strategies

Indexes are the second-most impactful optimization after N+1 resolution. This reference covers index types, design patterns, and when to use each.

## Index Types Overview

| Index Type | Use Case | Syntax |
|-----------|----------|--------|
| B-Tree (default) | Equality, range, sorting | `CREATE INDEX idx ON table (column)` |
| Hash | Exact match only (rarely used) | `CREATE INDEX idx ON table USING HASH (column)` |
| GIN | Arrays, JSONB, full-text search | `CREATE INDEX idx ON table USING GIN (column)` |
| GiST | Geospatial, full-text | `CREATE INDEX idx ON table USING GIST (column)` |
| BRIN | Sequential data, time-series | `CREATE INDEX idx ON table USING BRIN (column)` |
| Composite | Multi-column queries | `CREATE INDEX idx ON table (col1, col2)` |

## Index Design Patterns

### 1. Single Column Indexes
```sql
-- Simple equality
CREATE INDEX idx_users_email ON users (email);

-- For sorting (also covers WHERE)
CREATE INDEX idx_posts_created_at ON posts (created_at DESC);
```

### 2. Composite Indexes
```sql
-- Order matters! Left-to-right usage
CREATE INDEX idx_orders_user_status 
    ON orders (user_id, status);

-- Covers: WHERE user_id = ? AND status = ?
-- Covers: WHERE user_id = ?
-- Does NOT cover: WHERE status = ?
```

### 3. Partial Indexes (Filtered)
```sql
-- Only index active records
CREATE INDEX idx_active_posts ON posts (created_at) 
WHERE status = 'active';

-- Index for specific user type
CREATE INDEX idx_premium_orders ON orders (user_id) 
WHERE user_type = 'premium';
```

### 4. Covering Indexes (INCLUDE)
```sql
-- Store extra columns in index to avoid table lookup
CREATE INDEX idx_orders_id ON orders (id) 
INCLUDE (user_id, total, status);

-- Query can get all data from index
SELECT id, user_id, total FROM orders WHERE id = 1;
-- No table access needed!
```

### 5. Unique Indexes
```sql
CREATE UNIQUE INDEX idx_users_email ON users (email);
-- Automatically enforces uniqueness
```

## Query Analysis with EXPLAIN

### Basic Analysis
```sql
EXPLAIN ANALYZE 
SELECT * FROM users WHERE email = 'test@example.com';
```

### Understanding Output
- **Seq Scan**: Full table scan (BAD for large tables)
- **Index Scan**: Using an index (GOOD)
- **Index Only Scan**: Index contains all needed columns (BEST)
- **Bitmap**: Combining multiple indexes

### Example Output
```
Seq Scan on users (cost=0.00..4.50 rows=1 width=100)
  Filter: (email = 'test@example.com'::text)
  ->  Index Scan using idx_users_email on users
```

## When to Create Indexes

### 1. WHERE Clause Columns
```php
// Add index on frequently filtered columns
// In migration:
$table->index('status');       // WHERE status = 'active'
$table->index(['user_id', 'status']); // Composite for combined filters
```

### 2. ORDER BY Columns
```php
// Index covers ORDER BY
$table->index('created_at');      // ORDER BY created_at DESC

// Composite with WHERE
$table->index(['status', 'created_at']); // WHERE status = ? ORDER BY created_at
```

### 3. JOIN Keys
```php
// Always index foreign keys
$table->foreignId('user_id')->constrained(); // Adds index automatically
```

### 4. Foreign Keys (Implicit)
```php
// Laravel's constrained() adds index automatically
$table->foreignId('user_id')->constrained();
// Equivalent to:
// $table->index('user_id');
```

## Common Patterns

### 1. Status + Sort
```sql
-- Efficient for: WHERE status = ? ORDER BY created_at DESC
CREATE INDEX idx_posts_status_created 
ON posts (status, created_at DESC);
```

### 2. User + Type + Date
```sql
-- Efficient for: WHERE user_id = ? AND type = ? AND date > ?
CREATE INDEX idx_orders_user_type_date 
ON orders (user_id, type, date);
```

### 3. Polymorphic Relationships
```polymorphic``` tables don't need indexes on type_id/type columns:
```sql
-- Avoid indexing polymorphic columns (not selective)
-- Instead, add application-level filtering or use separate tables
```

## Index Maintenance

### Analyze Index Usage
```sql
-- Check if index is used
SELECT 
    indexrelname,
    idx_scan,
    idx_tup_read,
    idx_tup_fetch
FROM pg_stat_user_indexes
WHERE schemaname = 'public'
ORDER BY idx_scan DESC;
```

### Find Unused Indexes
```sql
SELECT 
    indexrelname,
    idx_scan
FROM pg_stat_user_indexes
WHERE idx_scan = 0
AND indexrelname NOT LIKE '%pkey%';
```

### Index Bloat
```sql
-- Check for bloated indexes
SELECT 
    schemaname || '.' || tablename AS table_name,
    indexname,
    pg_size_pretty(pg_relation_size(indexrelid)) AS index_size
FROM pg_stat_user_indexes
ORDER BY pg_relation_size(indexrelid) DESC;
```

## Performance Impact

| Scenario | Without Index | With Index | Improvement |
|----------|-------------|------------|-------------|
| Lookup by email (1M users) | 500ms | 2ms | 250x |
| ORDER BY date (100k rows) | 800ms | 15ms | 53x |
| WHERE + ORDER BY | 1200ms | 20ms | 60x |

## Migration Examples (Laravel)

```php
// Create index
Schema::create('posts', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained();
    $table->string('title');
    $table->timestamp('published_at');
    
    // Single column
    $table->index('published_at');
    
    // Composite
    $table->index(['user_id', 'published_at']);
    
    // Partial (raw)
    $table->raw('CREATE INDEX idx_active ON posts (published_at) WHERE status = \'active\'');
});
```

```php
// Add index to existing table
Schema::table('orders', function (Blueprint $table) {
    $table->index(['user_id', 'status']);
    $table->index(['status', 'created_at']);
});
```

```php
// Remove index
Schema::table('orders', function (Blueprint $table) {
    $table->dropIndex('orders_user_id_status_index');
});
```