# Installation

How to install Laravel Reverb in a Laravel project.

## Install Reverb Package

```bash
composer require laravel/reverb
```

## Publish Configuration

```bash
php artisan vendor:publish --provider="Laravel\Reverb\ReverbServiceProvider"
```

This creates `config/reverb.php` with default settings.

## Verify Installation

After installation, verify Reverb is properly installed:

```bash
php artisan reverb:install --help
```

## Troubleshooting

- **Port already in use**: Stop other WebSocket services or use a different port with `--port`
- **Extension missing**: Ensure PHP sockets extension is enabled
- **Permission denied**: Run the artisan command with appropriate permissions