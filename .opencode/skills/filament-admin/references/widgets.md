# Filament v5 Widgets & Advanced Customization

## Widgets
- **Stats**: `StatsOverviewWidget` using `Stat::make('Label', 'Value')`
- **Charts**: `ChartWidget` using `getOptions()` and `getData()`
- **Tables**: `TableWidget` for displaying data on dashboards.

## Custom Components
- **Blade Views**: Use `ViewField` or `ViewEntry` to render custom Blade templates.
- **Custom Classes**: Extend `Component` and implement `Entry` or `Field`.

## Integration & Security
- **Spatie Permissions**: Use `canAccess()` in Resources or `can()` in actions.
- **Performance**: Use `deferFilters()`, `with()` in queries, and `lazy()` loading for heavy components.
