# Docker Services

## Overview

The project uses Docker for additional services beyond the Laravel application.

## Services

| Service | Port | Purpose |
|---------|------|---------|
| PostgreSQL | 5432 | Primary database |
| Redis | 6379 | Cache & Queue |
| Evolution-go | 8080 | WhatsApp API |

## Docker Commands

```bash
# Start all services
docker-compose up -d

# Start specific service
docker-compose up -d postgres

# Check status
docker-compose ps

# View logs
docker-compose logs -f postgres

# Stop services
docker-compose down

# Rebuild containers
docker-compose up -d --build
```

## docker-compose.yml Structure

```yaml
version: '3.8'

services:
    postgres:
        image: postgres:16
        environment:
            POSTGRES_DB: saas
            POSTGRES_USER: user
            POSTGRES_PASSWORD: password
        ports:
            - "5432:5432"
        volumes:
            - postgres_data:/var/lib/postgresql/data

    redis:
        image: redis:7-alpine
        ports:
            - "6379:6379"

    evolution-go:
        image: atendai/evolution-api:latest
        ports:
            - "8080:8080"

volumes:
    postgres_data:
```

## Connecting from Laravel

```env
# .env
DB_CONNECTION=pgsql
DB_HOST=localhost
DB_PORT=5432
DB_DATABASE=saas
DB_USERNAME=user
DB_PASSWORD=password

REDIS_HOST=127.0.0.1
REDIS_PORT=6379
```