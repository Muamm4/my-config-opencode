# Troubleshooting

Common Docker issues and solutions for Laravel development.

## Container Issues

### Container won't start

**Symptoms**: `docker-compose up -d` fails or container exits immediately.

**Diagnosis**:
```bash
docker-compose logs [service]
docker-compose ps
```

**Solutions**:
- Check port conflicts: `docker-compose port [service] 80`
- Verify volume paths exist
- Check environment variables

### Permission denied

**Symptoms**: Permission errors in mounted volumes.

**Solutions**:
```bash
# Fix ownership
chown -R $(id -u):$(id -g) ./storage ./bootstrap/cache

# Or in container
docker-compose exec php-fpm chown -R www-data:www-data /var/www/html/storage
```

## Network Issues

### Can't connect to service

**Symptoms**: Connection refused between containers.

**Diagnosis**:
```bash
docker-compose exec php-fpm ping postgres
docker-compose exec php-fpm nc -zv postgres 5432
```

**Solutions**:
- Verify both services on same network
- Check service name resolution
- Verify exposed ports

## Database Issues

### PostgreSQL connection refused

**Symptoms**: Laravel can't connect to database.

**Diagnosis**:
```bash
docker-compose exec postgres psql -U user -d app
```

**Solutions**:
- Verify credentials in `docker-compose.yml` and `.env`
- Check `DB_HOST` equals service name (e.g., `postgres`)
- Ensure database is ready (wait for container to be healthy)

### Database data corruption

**Symptoms**: Database errors, table not found.

**Solutions**:
```bash
# Backup if possible
docker-compose exec postgres pg_dump -U user -d app > backup.sql

# Remove volume
docker-compose down -v
docker-compose up -d
```

## Performance Issues

### Slow containers

**Symptoms**: High memory usage, slow response.

**Solutions**:
- Limit container resources:
  ```yaml
  services:
    php-fpm:
      deploy:
        resources:
          limits:
            memory: 512M
  ```
- Clear unused volumes: `docker volume prune`

### Disk space low

**Solutions**:
```bash
# Clean containers
docker container prune

# Clean images
docker image prune

# Clean volumes
docker volume prune
```

## Common Errors

### "Bind for 0.0.0.0:80 failed: port is already allocated"

```bash
# Find what's using the port
lsof -i :80

# Change port in docker-compose.yml
ports:
  - "8080:80"
```

### " Cannot link to a running container"

- Use shared network instead of legacy links
- Ensure services on same `networks` section

### "Volume mount denied"

- Check SELinux/AppArmor settings
- Use absolute paths for volumes