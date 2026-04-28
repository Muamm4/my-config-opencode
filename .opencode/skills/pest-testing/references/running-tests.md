# Running Tests

Commands to run Pest tests.

## Basic Commands

```bash
# All tests (via Composer script)
composer test

# Run all tests directly
./vendor/bin/pest

# Unit tests only
./vendor/bin/pest --suite=unit

# Feature tests only
./vendor/bin/pest --suite=feature
```

## Selective Execution

```bash
# Single file
./vendor/bin/pest tests/Feature/DashboardTest.php

# Single test by name
./vendor/bin/pest --filter="dashboard"

# Single test by name (shorthand)
./vendor/bin/pest --name="can create post"
```

## Coverage

```bash
# With coverage report
./vendor/bin/pest --coverage

# Minimum coverage threshold
./vendor/bin/pest --coverage --min=80
```

## Watch Mode

```bash
# Auto-run tests on file changes
./vendor/bin/pest --watch
```

## Debugging

```bash
# Verbose output
./vendor/bin/pest --verbose

# Parallel execution
./vendor/bin/pest --parallel

# Stop on first failure
./vendor/bin/pest --stop-on-failure
```