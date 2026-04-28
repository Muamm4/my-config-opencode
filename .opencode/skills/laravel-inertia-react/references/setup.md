# Project Setup

## New Project with Starter Kit (Recommended)

```bash
# Laravel 13 + React + Inertia
composer create-project laravel/laravel example-app
cd example-app
composer require laravel/breeze --dev
php artisan breeze:install react
npm install
npm run dev
```

## Manual Setup

```bash
# Laravel
composer require inertiajs/inertia-laravel
php artisan inertia:middleware

# React
npm install @inertiajs/react @inertiajs/vite
npm install --save-dev @vitejs/plugin-react
```

## Vite Config (vite.config.ts)

```typescript
import { defineConfig } from 'vite'
import laravel from 'laravel-vite-plugin'
import inertia from '@inertiajs/vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
    plugins: [
        laravel({ input: 'resources/js/app.tsx' }),
        inertia(),
        react()
    ],
})
```

## App Entry (resources/js/app.tsx)

```typescript
import { createInertiaApp } from '@inertiajs/react'

createInertiaApp()
```

## Root Template (resources/views/app.blade.php)

```blade
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>{{ config('app.name') }}</title>
    @viteHead()
</head>
<body>
    @inertia()
</body>
</html>
```