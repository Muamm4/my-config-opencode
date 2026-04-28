# Docker Services

Typical Laravel stack services configuration for docker-compose.

## Standard Services

### PHP-FPM
```yaml
php-fpm:
  image: laravel/php:8.3-fpm
  volumes:
    - ./:/var/www/html
  networks:
    - laravel
```

### NGINX
```yaml
nginx:
  image: nginx:alpine
  ports:
    - "80:80"
  volumes:
    - ./:/var/www/html
    - ./nginx.conf:/etc/nginx/conf.d/default.conf
  depends_on:
    - php-fpm
  networks:
    - laravel
```

### PostgreSQL
```yaml
postgres:
  image: postgres:16
  environment:
    POSTGRES_DB: app
    POSTGRES_USER: user
    POSTGRES_PASSWORD: password
  volumes:
    - postgres_data:/var/lib/postgresql/data
  networks:
    - laravel
```

### Redis
```yaml
redis:
  image: redis:7-alpine
  ports:
    - "6379:6379"
  networks:
    - laravel
```

### Node
```yaml
node:
  image: node:20
  volumes:
    - ./:/var/www/html
  working_dir: /var/www/html
  command: npm run dev
  networks:
    - laravel
```

## Networks Configuration

```yaml
networks:
  laravel:
    driver: bridge
```

## Volumes

```yaml
volumes:
  postgres_data:
```

## Complete Example

```yaml
version: '3.8'

services:
  php-fpm:
    image: laravel/php:8.3-fpm
    volumes:
      - ./:/var/www/html
    networks:
      - laravel

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./:/var/www/html
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - php-fpm
    networks:
      - laravel

  postgres:
    image: postgres:16
    environment:
      POSTGRES_DB: app
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - laravel

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    networks:
      - laravel

  node:
    image: node:20
    volumes:
      - ./:/var/www/html
    working_dir: /var/www/html
    command: npm run dev
    networks:
      - laravel

networks:
  laravel:
    driver: bridge

volumes:
  postgres_data:
```