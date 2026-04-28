# Filament v5 Tables

## Columns
- **Text**: `TextColumn::make('name')->searchable()->sortable()->limit(50)->wrap()`
- **Visual**: `IconColumn::make('is_active')->boolean()`, `BadgeColumn::make('status')->colors([...])`, `ImageColumn::make('avatar')->circular()`
- **Data**: `MoneyColumn::make('price')->currency('USD')`, `CountColumn::make('comments')`

## Filtering & Actions
- **Filters**: `SelectFilter`, `TernaryFilter`, `Filter::make('name')->query(...)`
- **Record Actions**: `recordActions([ EditAction::make(), DeleteAction::make() ])`
- **Toolbar Actions**: `toolbarActions([ BulkActionGroup::make([ DeleteBulkAction::make() ]) ])`
- **Deferred Filters**: `deferFilters()` (v5 default) improves performance by not reloading on every filter change.
