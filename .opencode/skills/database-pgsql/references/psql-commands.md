# psql CLI Commands

## Connect to Database

```bash
# Connect to database
psql -U postgres -d saas

# Connect with host/port
psql -h 127.0.0.1 -p 5432 -U postgres -d saas

# Connect with password (viaPGPASSWORD env)
PGPASSWORD=secret psql -U postgres -d saas
```

## Navigation Commands

| Command | Description |
|---------|-------------|
| `\l` | List all databases |
| `\dt` | List all tables |
| `\di` | List all indexes |
| `\dv` | List all views |
| `\df` | List all functions |
| `\d` | List all objects |
| `\d table_name` | Describe table |
| `\d index_name` | Describe index |
| `\dn` | List schemas |
| `\du` | List users/roles |

## Query Execution

```sql
-- Run SQL query
SELECT * FROM users LIMIT 10;

-- Execute from file
\i /path/to/query.sql

-- Show query timing
\timing on

-- Output formatting
\a       # Align output
\x       # Expanded display
\pset format aligned
```

## Information Queries

```sql
-- Database size
SELECT pg_size_pretty(pg_database_size('saas'));

-- Table sizes
SELECT pg_size_pretty(pg_total_relation_size('users'));

-- Connection info
SELECT version();

-- Current user
SELECT current_user;

-- Current database
SELECT current_database();
```

## Common psql Shortcuts

```bash
# Export to CSV
psql -U postgres -d saas -c "COPY (SELECT * FROM users) TO STDOUT WITH CSV" > users.csv

# Execute single query
psql -U postgres -d saas -t -A -c "SELECT email FROM users WHERE active = true"
```