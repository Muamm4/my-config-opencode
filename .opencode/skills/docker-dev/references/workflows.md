# Docker Workflows

Common development workflows using Docker for Laravel projects.

## Project Setup

### 1. Initialize new Laravel project with Docker

```bash
# Create project directory
mkdir my-laravel-app && cd my-laravel-app

# Create docker-compose.yml
touch docker-compose.yml

# Start services
docker-compose up -d
```

### 2. Connect existing project to Docker

```bash
# Copy docker-compose.yml to project root
# Ensure volume paths match project structure

# Start services
docker-compose up -d

# Verify connectivity
docker-compose ps
```

## Development Workflows

### Running Laravel Commands

```bash
# Artisan commands
docker-compose exec php-fpm php artisan migrate
docker-compose exec php-fpm php artisan db:seed
docker-compose exec php-fpm php artisan make:model User -m
```

### Running Tests

```bash
# PHPUnit via Docker
docker-compose exec php-fpm ./vendor/bin/phpunit

# Pest PHP
docker-compose exec php-fpm ./vendor/bin/pest
```

### Database Operations

```bash
# Connect to PostgreSQL
docker-compose exec postgres psql -U user -d app

# Import database
docker-compose exec -T postgres psql -U user -d app < dump.sql

# Export database
docker-compose exec postgres pg_dump -U user app > dump.sql
```

### Node/Frontend Workflows

```bash
# Install dependencies
docker-compose exec node npm install

# Build assets
docker-compose exec node npm run build

# Watch mode
docker-compose exec node npm run dev
```

## Maintenance

### Backup & Restore

```bash
# Backup database
docker-compose exec postgres pg_dump -U user -d app > backup.sql

# Restore database
docker-compose exec -T postgres psql -U user -d app < backup.sql
```

### Clean rebuild

```bash
# Stop and remove volumes
docker-compose down -v

# Rebuild images
docker-compose build --no-cache

# Start fresh
docker-compose up -d
```

## Troubleshooting

### Check logs for errors

```bash
# All services
docker-compose logs

# Specific service
docker-compose logs nginx
```

### Restart a service

```bash
docker-compose restart php-fpm
```

### Access container shell for debugging

```bash
docker-compose exec php-fpm sh
```