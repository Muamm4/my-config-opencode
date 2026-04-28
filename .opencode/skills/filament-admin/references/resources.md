# Filament v5 Resources & Pages

Resources coordinate the model, table, and form.

## Resource Lifecycle & Structure
```bash
# Create resource
php artisan make:filament-resource Post
# With auto-generated schemas from model
php artisan make:filament-resource Post --generate
# With relation managers
php artisan make:filament-resource Post --relation-managers
```

## Resource Definition Pattern
```php
class PostResource extends Resource
{
    protected static ?string $model = Post::class;
    protected static ?string $navigationIcon = 'heroicon-o-document-text';
    protected static ?string $navigationGroup = 'Content';
    protected static ?int $navigationSort = 1;
    protected static ?string $recordTitleAttribute = 'title';

    public static function form(Schema $schema): Schema { ... }
    public static function table(Table $table): Table { ... }
    public static function getPages(): array { ... }
}
```

## Relation Managers
```php
class CommentsRelationManager extends RelationManager
{
    protected static string $relationship = 'comments';

    public function form(Schema $schema): Schema { ... }
    public function table(Table $table): Table { ... }
}
```

## Custom Pages & Clusters
- **Custom Pages**: `php artisan make:filament-page Settings --resource=PostResource`
- **Clusters**: Group related resources under a sub-navigation.
  ```php
  class SalesCluster extends Cluster { 
      protected static string $navigationIcon = 'heroicon-o-chart-bar';
  }
  // In Resource:
  protected static ?string $cluster = SalesCluster::class;
  ```
