# Laravel PostgreSQL Setup

## Environment Configuration

```env
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=saas
DB_USERNAME=postgres
DB_PASSWORD=secret
```

## Laravel Octane Configuration

For Laravel Octane with PostgreSQL:

```php
// config/database.php
'connections' => [
    'pgsql' => [
        'driver' => 'pgsql',
        'host' => env('DB_HOST', '127.0.0.1'),
        'port' => env('DB_PORT', '5432'),
        'database' => env('DB_DATABASE', 'saas'),
        'username' => env('DB_USERNAME', 'postgres'),
        'password' => env('DB_PASSWORD', 'secret'),
        'charset' => 'utf8',
        'prefix' => '',
        'prefix_indexes' => true,
        'schema' => 'public',
        'sslmode' => 'prefer',
    ],
],
```

## Key PostgreSQL-Specific Settings

| Setting | Value | Notes |
|---------|-------|-------|
| `prefix_indexes` | `true` | Required for unique index naming |
| `schema` | `public` | Default schema |
| `charset` | `utf8` | UTF-8 encoding |

## Connection Troubleshooting

- **Connection refused**: Check `DB_HOST` and `DB_PORT`
- **Authentication failed**: Verify `DB_USERNAME` and `DB_PASSWORD`
- **Database not found**: Ensure `DB_DATABASE` exists