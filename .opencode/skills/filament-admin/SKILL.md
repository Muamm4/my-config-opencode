# filament-admin

## Identity
You are a Filament PHP specialist for building admin panels.

## Installation

```bash
# Install Filament
composer require filament/filament

# Install in existing Laravel project
php artisan filament:install

# Create admin user
php artisan make:filament-user
```

## Creating Resources

```bash
# Create resource
php artisan make:filament-resource Post

# With Page
php artisan make:filament-resource Post --generate

# With Relation Manager
php artisan make:filament-resource Post --relation-managers
```

## Resource Structure

```
app/Filament/Resources/
├── PostResource.php
├── Pages/
│   ├── ListPosts.php
│   ├── CreatePost.php
│   ├── EditPost.php
│   └── ViewPost.php
└── RelationManagers/
    └── CommentsRelationManager.php
```

## Resource Definition

```php
<?php
namespace App\Filament\Resources;

use Filament\Forms;
use Filament\Tables;
use Filament\Resources\Resource;
use Filament\Resources\RelationManagers\CommentsRelationManager;
use App\Models\Post;

class PostResource extends Resource
{
    protected static ?string $model = Post::class;

    protected static string $slug = 'posts';

    protected static ?string $recordTitleAttribute = 'title';

    public static function form(Forms\Form $form): Forms\Form
    {
        return $form
            ->schema([
                Forms\Components\TextInput::make('title')->required(),
                Forms\Components\RichEditor::make('body'),
                Forms\Components\Toggle::make('published'),
            ]);
    }

    public static function table(Tables\Table $table): Tables\Table
    {
        return $table
            ->columns([
                Tables\Columns\TextColumn::make('title')->searchable(),
                Tables\Columns\BooleanColumn::make('published'),
                Tables\Columns\TextColumn::make('created_at')->dateTime(),
            ])
            ->filters([
                Tables\Filters\SelectFilter::make('published'),
            ])
            ->actions([
                Tables\Actions\EditAction::make(),
                Tables\Actions\DeleteAction::make(),
            ])
            ->bulkActions([
                Tables\Actions\DeleteBulkAction::make(),
            ]);
    }

    public static function getRelations(): array
    {
        return [
            CommentsRelationManager::class,
        ];
    }

    public static function pages(): array
    {
        return [
            'index' => ListPosts::route('/'),
            'create' => CreatePost::route('/create'),
            'edit' => EditPost::route('/{record}/edit'),
        ];
    }
}
```

## Form Components

```php
Forms\Components\TextInput::make('title')->required()
Forms\Components\Textarea::make('description')
Forms\Components\RichEditor::make('body')
Forms\Components\Toggle::make('published')
Forms\Components\Select::make('category_id')->relationship('category', 'title')
Forms\Components\MultiSelect::make('tags')->relationship('tags', 'title')
Forms\Components\DatePicker::make('published_at')
Forms\Components\DateTimePicker::make('scheduled_at')
Forms\Components\FileUpload::make('attachment')
Forms\Components\ImageUpload::make('image')
Forms\Components\Checkbox::make('is_active')
Forms\Components\Radio::make('status')->options([
    'draft' => 'Draft',
    'published' => 'Published',
])
```

## Table Columns

```php
Tables\Columns\TextColumn::make('title')->searchable()->sortable()
Tables\Columns\TextAreaColumn::make('excerpt')->limit(50)
Tables\Columns\BooleanColumn::make('is_active')
Tables\Columns\ToggleColumn::make('is_active')
Tables\Columns\ImageColumn::make('image')
Tables\Columns\AvatarColumn::make('avatar')
Tables\Columns\BadgeColumn::make('status')->colors([
    'draft' => 'gray',
    'published' => 'green',
])
Tables\Columns\CountColumn::make('comments')
Tables\Columns\RelationshipCountColumn::make('comments')
```

## Relation Manager

```php
<?php
namespace App\Filament\Resources\PostResource\RelationManagers;

use Filament\Resources\RelationManager;
use Filament\Tables;
use Filament\Tables\Table;
use App\Models\Comment;

class CommentsRelationManager extends RelationManager
{
    protected static string $relationship = 'comments';

    public function table(Table $table): Table
    {
        return $table
            ->columns([
                Tables\Columns\TextColumn::make('user.name'),
                Tables\Columns\TextColumn::make('body')->limit(50),
                Tables\Columns\TextColumn::make('created_at')->dateTime(),
            ])
            ->actions([
                Tables\Actions\EditAction::make(),
                Tables\Actions\DeleteAction::make(),
            ]);
    }
}
```

## Custom Pages

```php
php artisan make:filament-page Settings

// In resource
public static function pages(): array
{
    return [
        'index' => ListPosts::route('/'),
        'settings' => Settings::route('/settings'),
    ];
}
```

## Widgets

```php
php artisan make:filament-widget StatsOverview

// In resource
public static function getWidgets(): array
{
    return [
        StatsOverview::class,
    ];
}
```

## Spatie Permission Integration

```bash
composer require spatie/filament-permissions
```

```php
// Add to resource
use Filament\Facades\Filament;

public static function canAccess(): bool
{
    return auth()->user()->can('manage_posts');
}
```

## Global Search

```php
// Enable in ServiceProvider
Filament::scaffoldPages();

protected function getPages(): array
{
    return [
        'dashboard' => Pages\Dashboard::route('/'),
    ];
}
```

## Trigger Conditions
- When user asks about admin panel
- When mentioning Filament
- When needing CRUD admin
- When asking about admin resources