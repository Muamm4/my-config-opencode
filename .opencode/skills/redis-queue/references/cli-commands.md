# Redis CLI Commands

## Basic Connection

```bash
# Test connection
redis-cli ping
# PONG

# Select database
redis-cli SELECT 0  # Database 0-15

# Info
redis-cli INFO
redis-cli INFOclients
redis-cli INFO memory
```

## Key Operations

```bash
# Set/Get
redis-cli SET mykey "value"
redis-cli GET mykey

# Set with TTL
redis-cli SETEX mykey 300 "value"  # 300 seconds
redis-cli PSETEX mykey 300000 "value"  # 300000 ms

# Get with pattern
redis-cli KEYS "laravel:*"
redis-cli KEYS "*:cache:*"

# Delete
redis-cli DEL mykey
redis-cli DEL cache:key1 cache:key2

# Check if exists
redis-cli EXISTS mykey

# TTL
redis-cli TTL mykey
redis-cli EXPIRE mykey 300
redis-cli PERSIST mykey  # Remove TTL
```

## Hash Operations

```bash
# Hash
redis-cli HSET user:1 name "John"
redis-cli HGET user:1 name
redis-cli HGETALL user:1

# List
redis-cli LPUSH queue:default "job1"
redis-cli RPOP queue:default
```

## Queue Operations

```bash
# List length
redis-cli LLEN queue:default

# View first/last
redis-cli LRANGE queue:default 0 -1
```

## Data Management

```bash
# Flush (use with caution!)
redis-cli FLUSHDB  # Current database
redis-cli FLUSHALL  # All databases

# Slow log
redis-cli SLOWLOG GET 10
```

## Common Pitfalls

1. **KEYS in production**: Never use `KEYS` on large datasets - use `SCAN` instead
2. **FLUSHDB**: Always confirm database number before flushing
3. **Memory**: Monitor with `redis-cli INFO memory`