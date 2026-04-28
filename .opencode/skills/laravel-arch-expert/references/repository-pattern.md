# Repository Pattern

## Overview

The Repository pattern provides an abstraction layer between the domain and data persistence. It allows you to swap data sources without changing domain logic.

## Repository Interface (Domain)

```php
<?php
// app/Domain/Repositories/UserRepositoryInterface.php

namespace App\Domain\Repositories;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\UserId;
use App\Domain\ValueObjects\Email;

interface UserRepositoryInterface
{
    public function findById(UserId $id): ?User;
    
    public function findByEmail(Email $email): ?User;
    
    public function findByIds(array $ids): iterable;
    
    public function save(User $user): void;
    
    public function delete(UserId $id): void;
    
    public function all(): iterable;
    
    public function count(): int;
    
    public function paginate(int $page, int $perPage): \Illuminate\Contracts\Pagination\LengthAwarePaginator;
}
```

## Repository Implementation (Infrastructure)

```php
<?php
// app/Infrastructure/Persistence/Repositories/EloquentUserRepository.php

namespace App\Infrastructure\Persistence\Repositories;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Repositories\UserRepositoryInterface;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;
use App\Infrastructure\Persistence\Mappers\UserMapper;

class EloquentUserRepository implements UserRepositoryInterface
{
    public function __construct(
        private UserMapper $mapper
    ) {}

    public function findById(UserId $id): ?User
    {
        $model = UserModel::find($id->value());
        
        return $model ? $this->mapper->toEntity($model) : null;
    }

    public function findByEmail(Email $email): ?User
    {
        $model = UserModel::where('email', $email->value())->first();
        
        return $model ? $this->mapper->toEntity($model) : null;
    }

    public function findByIds(array $ids): iterable
    {
        $models = UserModel::findMany($ids);
        
        return $models->map(fn($model) => $this->mapper->toEntity($model));
    }

    public function save(User $user): void
    {
        $model = $this->mapper->toModel($user);
        
        $model->save();
    }

    public function delete(UserId $id): void
    {
        UserModel::destroy($id->value());
    }

    public function all(): iterable
    {
        return UserModel::all()
            ->map(fn($model) => $this->mapper->toEntity($model));
    }

    public function count(): int
    {
        return UserModel::count();
    }

    public function paginate(int $page, int $perPage): \Illuminate\Contracts\Pagination\LengthAwarePaginator
    {
        return UserModel::paginate($perPage);
    }
}
```

## Entity-Model Mapper

```php
<?php
// app/Infrastructure/Persistence/Mappers/UserMapper.php

namespace App\Infrastructure\Persistence\Mappers;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;

class UserMapper
{
    public function toEntity(UserModel $model): User
    {
        return User::reconstitute(
            UserId::fromString($model->id),
            Email::fromString($model->email),
            $model->name,
            $model->is_active,
            \DateTimeImmutable::createFromFormat(
                'Y-m-d H:i:s',
                $model->created_at
            )
        );
    }

    public function toModel(User $entity): UserModel
    {
        $id = $entity->id()->value();
        
        $model = UserModel::findOrNew($id);
        
        $model->email = $entity->email()->value();
        $model->name = $entity->name();
        $model->is_active = $entity->isActive();
        
        return $model;
    }
}
```

## Repository vs Eloquent: When to Use Which

| Scenario | Use Repository | Use Eloquent Directly |
|----------|---------------|---------------------|
| **Multiple data sources** | Yes | No |
| **Need to swap DB** | Yes | No |
| **Heavy querying logic** | Yes | No |
| **Simple CRUD** | No | Yes |
| **Prototyping/MVP** | No | Yes |
| **Testing with fakes** | Yes | No |
| **Complex domain logic** | Yes | No |

## Decision Tree

```
Is the data from a single source?
├── YES → Use Eloquent directly
└── NO → Is the domain complex?
        ├── YES → Use Repository pattern
        └── NO → Consider simpler abstraction
```

## Service Provider Binding

```php
<?php
// app/Providers/DomainServiceProvider.php

namespace App\Providers;

use App\Domain\Repositories\UserRepositoryInterface;
use App\Infrastructure\Persistence\Repositories\EloquentUserRepository;
use Illuminate\Support\ServiceProvider;

class DomainServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        $this->app->singleton(
            UserRepositoryInterface::class,
            EloquentUserRepository::class
        );
    }
}
```