# Laravel Pest Setup

Installation and initial configuration for Pest in Laravel projects.

## Installation

```bash
# Install Pest core
composer require pestphp/pest --dev

# Install Laravel plugin
composer require pestphp/pest-plugin-laravel --dev
```

## Initialize Pest

```bash
# Run Pest installer (creates tests/Pest.php)
php artisan pest:install
```

## Create Test Files

```bash
# Generate a test file
php artisan pest:test Post

# This creates tests/Feature/PostTest.php with base template
```

## Test Directory Structure

```
tests/
├── Pest.php              # Global setup (beforeEach, hooks)
├── Unit/
│   └── ExampleTest.php
└── Feature/
    ├── ExampleTest.php
    └── PostTest.php
```

## Pest.php Global Setup

```php
<?php
// tests/Pest.php

beforeEach(function () {
    // Run before each test
    DatabaseMigrations::init();
});

afterEach(function () {
    // Run after each test
    Cache::flush();
});
```

## Configuration (phpunit.xml)

Pest uses `phpunit.xml`. Default configuration:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="./vendor/phpunit/phpunit/phpunit.xsd"
         bootstrap="vendor/autoload.php"
         colors="true">
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">./tests/Unit</directory>
        </testsuite>
        <testsuite name="Feature">
            <directory suffix="Test.php">./tests/Feature</directory>
        </testsuite>
    </testsuites>
</phpunit>
```