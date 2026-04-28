# Docker Commands

Common Docker and docker-compose commands for Laravel development environment.

## Service Lifecycle

### Start Services
```bash
docker-compose up -d
```

### Stop Services
```bash
docker-compose down
```

### Restart Services
```bash
docker-compose restart [service]
```

## Logs & Monitoring

### View Logs
```bash
# All services
docker-compose logs -f

# Specific service
docker-compose logs -f [service]
```

### Follow logs in real-time
```bash
docker-compose logs -f --tail=100 [service]
```

## Container Management

### Rebuild Containers
```bash
# Full rebuild (no cache)
docker-compose build --no-cache

# Rebuild specific service
docker-compose build [service]
```

### Execute Commands in Container

```bash
# Enter shell
docker-compose exec [service] bash

# Enter PHP container
docker-compose exec php-fpm sh

# Run npm in Node container
docker-compose exec node npm run dev

# PostgreSQL CLI
docker-compose exec postgres psql -U [user] -d [db]
```

## Common Patterns

### Health Check
```bash
docker-compose ps
```

### Inspect Container
```bash
docker-compose exec [service] cat /etc/os-release
```

### Copy files from container
```bash
docker-compose cp [service]:/path/to/file /local/path
```

## Troubleshooting

### Rebuild from scratch
```bash
docker-compose down -v
docker-compose build --no-cache
docker-compose up -d
```

### Check container status
```bash
docker-compose ps
docker inspect [container_name]
```