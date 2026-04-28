# Filament v5 Infolists, Notifications & Actions

## Infolists (Read-only)
Used in `View` pages or custom components.
- **Entries**: `TextEntry`, `ImageEntry`, `IconEntry`, `ColorEntry`, `KeyValueEntry`, `RepeatableEntry`
- **Layout**: Same as Forms (`Grid`, `Section`, `Tabs`).

## Notifications
```php
Notification::make()
    ->title('Success')
    ->success()
    ->body('Operation completed.')
    ->actions([
        Action::make('undo')->dispatch('undoEvent'),
    ])
    ->send();
```

## Global & Modal Actions
- **Global Actions**: Defined in `getGlobalActions()` in Resource.
- **Modal Forms**: `Action::make('name')->form([ ... ])->action(fn (array $data) => ...)`
- **Wizard Modals**: `Action::make('name')->steps([ Step::make(...) ])`
- **Confirmation**: `requiresConfirmation()`
