# Filament v5 Panels & Panel Providers

PanelProviders serve as the central configuration hub for Filament admin panels.

## Panel Configuration Example
```php
public function panel(Panel $panel): Panel
{
    return $panel
        ->id('admin')
        ->path('admin')
        ->login()
        ->registration()
        ->passwordReset()
        ->emailVerification()
        ->profile()
        ->colors([
            'primary' => Color::Blue,
            'danger' => Color::Red,
        ])
        ->brandName('My App')
        ->brandLogo(asset('images/logo.svg'))
        ->favicon(asset('images/favicon.ico'))
        ->maxContentWidth(Width::SevenExtraLarge)
        ->sidebarCollapsibleOnDesktop()
        ->sidebarFullyCollapsibleOnDesktop()
        ->spa() // Single Page Application mode
        ->unsavedChangesAlerts()
        ->databaseTransactions()
        ->resources([ PostResource::class ])
        ->pages([ Dashboard::class, Settings::class ])
        ->widgets([ StatsOverview::class ])
        ->middleware([
            \Illuminate\Cookie\Middleware\EncryptCookies::class,
            \Illuminate\Session\Middleware\StartSession::class,
        ])
        ->authMiddleware([
            \Filament\Http\Middleware\Authenticate::class,
        ]);
}
```

## Key Configuration Options
- **Branding**: `brandName()`, `brandLogo()`, `favicon()`, `colors()`.
- **Auth**: `login()`, `registration()`, `passwordReset()`, `emailVerification()`, `profile()`.
- **UI/UX**: `maxContentWidth()`, `sidebarCollapsibleOnDesktop()`, `spa()`.
- **Behavior**: `unsavedChangesAlerts()`, `databaseTransactions()`.
