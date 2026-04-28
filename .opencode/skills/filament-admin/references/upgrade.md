# Filament v5 Upgrade Guide

## Upgrading from v3/v4 to v5
```bash
# Step 1: Run automated upgrade script
composer require filament/upgrade:"^5.0" -W --dev
vendor/bin/filament-v5

# Step 2: Update dependencies
composer require filament/filament:"^5.0" -W --no-update
composer update

# Step 3: Cleanup
composer remove filament/upgrade --dev
```

## Breaking Changes to Note
- **Unique Validation**: Now ignores current record by default. Use `ignoreRecord: false` to disable.
- **Table Filters**: Now deferred by default. Use `deferFilters(false)` to revert.
- **Tailwind**: Migration to v4 is required.
