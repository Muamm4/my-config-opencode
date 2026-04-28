# PostgreSQL Diagnostic Queries

## Table Analysis

### Find Large Tables

```sql
SELECT
    relname AS table_name,
    pg_size_pretty(pg_total_relation_size(relid)) AS size
FROM pg_catalog.pg_class
WHERE relkind = 'r'
  AND relname NOT LIKE 'pg_%'
ORDER BY pg_total_relation_size(relid) DESC
LIMIT 10;
```

### Table Row Counts

```sql
SELECT
    relname AS table_name,
    n_live_tup AS row_count
FROM pg_stat_user_tables
ORDER BY n_live_tup DESC
LIMIT 10;
```

### Table Bloat Analysis

```sql
SELECT
    schemaname,
    tablename,
    pg_size_pretty(bloat) AS bloat,
    ratio
FROM (
    SELECT
        schemaname,
        tablename,
        ( CASE WHEN otta = 0 THEN 0 ELSE bt / otta * 100 END ) AS ratio,
        pg_size_pretty(bt) AS bloat
    FROM (
        SELECT
            schemaname,
            tablename,
            otta,
            bt,
            CASE WHEN otta > 0
                THEN bt / otta
                ELSE 0
            END AS ratio
        FROM (
            SELECT
                schemaname,
                tablename,
                SUM(pg_table_size(schemaname||'.'||tablename)) AS bt,
                SUM((pgstattuple(schemaname||'.'||tablename)).tuple_len) AS otta
            FROM pg_tables
            WHERE schemaname NOT IN ('pg_catalog', 'information_schema')
            GROUP BY schemaname, tablename
        ) AS sub
    ) AS sub2
) AS sub3
ORDER BY ratio DESC
LIMIT 10;
```

## Query Performance

### Slow Queries (requires pg_stat_statements)

```sql
SELECT
    query,
    calls,
    mean_time,
    total_time,
    rows,
    shared_blks_hit,
    shared_blks_read
FROM pg_stat_statements
ORDER BY mean_time DESC
LIMIT 10;
```

### Enable pg_stat_statements

```sql
CREATE EXTENSION IF NOT EXISTS pg_stat_statements;
```

### Query Planning Analysis

```sql
EXPLAIN (ANALYZE, BUFFERS, FORMAT TEXT)
SELECT * FROM users WHERE email = 'test@example.com';
```

## Connection Monitoring

### Active Connections

```sql
SELECT
    count(*) AS total,
    state,
    state_change
FROM pg_stat_activity
WHERE datname = current_database()
GROUP BY state, state_change;
```

### Long-Running Queries

```sql
SELECT
    pid,
    usename,
    application_name,
    state,
    query,
    NOW() - state_change AS duration,
    wait_event_type,
    wait_event
FROM pg_stat_activity
WHERE state != 'idle'
  AND query_start < NOW() - INTERVAL '5 seconds'
ORDER BY query_start;
```

### Connections by User

```sql
SELECT
    usename,
    count(*) AS connections
FROM pg_stat_activity
GROUP BY usename;
```

## Index Analysis

### Unused Indexes

```sql
SELECT
    schemaname,
    tablename,
    indexname,
    idx_scan,
    idx_tup_read,
    idx_tup_fetch
FROM pg_stat_user_indexes
WHERE idx_scan = 0
  AND idx_tup_read = 0
ORDER BY pg_relation_size(indexrelid);
```

### Index Hit Ratio

```sql
SELECT
    schemaname,
    tablename,
    indexname,
    idx_blks_hit,
    idx_blks_read,
    CASE WHEN idx_blks_hit + idx_blks_read = 0
        THEN 0
        ELSE idx_blks_hit::numeric / (idx_blks_hit + idx_blks_read)
    END AS hit_ratio
FROM pg_stat_user_indexes
ORDER BY hit_ratio;
```