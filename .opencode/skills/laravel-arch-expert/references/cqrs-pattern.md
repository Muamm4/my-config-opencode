# CQRS Pattern (Command Query Responsibility Segregation)

## Overview

CQRS separates read and write operations into distinct models. Commands modify state; queries retrieve data. This separation allows optimizing each operation independently.

## Why CQRS?

| Aspect | Traditional | CQRS |
|--------|-------------|------|
| **Model** | Single model for read/write | Separate models |
| **Optimization** | Compromise between both | Optimized per use case |
| **Complexity** | Simpler initially | More complex |
| **Scalability** | Same for reads/writes | Independent scaling |

## CQRS in Laravel

### Command (Write) Structure

```php
<?php
// app/Application/User/Commands/CreateUserCommand.php

namespace App\Application\User\Commands;

use App\Application\User\DTOs\CreateUserInput;
use App\Application\User\DTOs\UserDTO;
use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\Repositories\UserRepositoryInterface;

class CreateUserCommand
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function execute(CreateUserInput $input): UserDTO
    {
        // Validation logic
        $existingUser = $this->userRepository->findByEmail($input->email);
        
        if ($existingUser) {
            throw new \InvalidArgumentException('Email already exists');
        }
        
        // Create domain entity
        $user = User::create(
            $input->email,
            $input->name
        );
        
        // Persist
        $this->userRepository->save($user);
        
        // Return DTO
        return UserDTO::fromEntity($user);
    }
}
```

### Query (Read) Structure

```php
<?php
// app/Application/User/Queries/GetUserByIdQuery.php

namespace App\Application\User\Queries;

use App\Application\User\DTOs\UserDTO;
use App\Domain\ValueObjects\UserId;
use App\Domain\Repositories\UserRepositoryInterface;

class GetUserByIdQuery
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function execute(string $id): ?UserDTO
    {
        $userId = UserId::fromString($id);
        $user = $this->userRepository->findById($userId);
        
        return $user ? UserDTO::fromEntity($user) : null;
    }
}
```

```php
<?php
// app/Application/User/Queries/GetUserListQuery.php

namespace App\Application\User\Queries;

use App\Application\User\DTOs\UserListDTO;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;

class GetUserListQuery
{
    public function execute(int $page = 1, int $perPage = 15): UserListDTO
    {
        $paginator = UserModel::paginate($perPage);
        
        return UserListDTO::fromPaginator($paginator);
    }
}
```

## Command Handler Pattern

```php
<?php
// app/Application/User/Commands/Handlers/CreateUserHandler.php

namespace App\Application\User\Commands\Handlers;

use App\Application\User\Commands\CreateUserCommand;
use App\Application\User\DTOs\CreateUserInput;
use App\Application\User\DTOs\UserDTO;

class CreateUserHandler
{
    public function handle(CreateUserCommand $command, CreateUserInput $input): UserDTO
    {
        return $command->execute($input);
    }
}
```

## Controller Integration

```php
<?php
// app/Interfaces/Http/Controllers/UserController.php

namespace App\Interfaces\Http\Controllers;

use App\Http\Requests\CreateUserRequest;
use App\Application\User\Commands\CreateUserCommand;
use App\Application\User\Queries\GetUserByIdQuery;
use App\Application\User\Queries\GetUserListQuery;
use App\Application\User\DTOs\CreateUserInput;
use App\Http\Resources\UserResource;
use App\Http\Resources\UserCollectionResource;

class UserController
{
    // Command - Write
    public function store(CreateUserRequest $request)
    {
        $input = CreateUserInput::fromRequest($request);
        
        $command = app(CreateUserCommand::class);
        $userDto = $command->execute($input);
        
        return response()->json([
            'data' => $userDto->toArray(),
        ], 201);
    }
    
    // Query - Read
    public function index()
    {
        $query = app(GetUserListQuery::class);
        $userList = $query->execute(
            request()->query('page', 1),
            request()->query('per_page', 15)
        );
        
        return new UserCollectionResource($userList);
    }
    
    // Query - Read
    public function show(string $id)
    {
        $query = app(GetUserByIdQuery::class);
        $userDto = $query->execute($id);
        
        if (!$userDto) {
            return response()->json(['error' => 'User not found'], 404);
        }
        
        return new UserResource($userDto);
    }
}
```

## Read Models (Projections)

For complex read scenarios, create dedicated read models:

```php
<?php
// app/Application/User/Queries/Projections/UserWithOrdersProjection.php

namespace App\Application\User\Queries\Projections;

class UserWithOrdersProjection
{
    public function __construct(
        public readonly string $id,
        public readonly string $email,
        public readonly string $name,
        public readonly int $orderCount,
        public readonly string $totalSpent,
        public readonly string $lastOrderDate
    ) {}

    public static function fromRaw(array $row): self
    {
        return new self(
            $row['id'],
            $row['email'],
            $row['name'],
            (int) $row['order_count'],
            $row['total_spent'],
            $row['last_order_date']
        );
    }

    public function toArray(): array
    {
        return [
            'id' => $this->id,
            'email' => $this->email,
            'name' => $this->name,
            'orderCount' => $this->orderCount,
            'totalSpent' => $this->totalSpent,
            'lastOrderDate' => $this->lastOrderDate,
        ];
    }
}
```

```php
<?php
// app/Application/User/Queries/GetUserWithOrdersQuery.php

namespace App\Application\User\Queries;

use App\Application\User\Queries\Projections\UserWithOrdersProjection;
use Illuminate\Support\Facades\DB;

class GetUserWithOrdersQuery
{
    public function execute(string $userId): ?UserWithOrdersProjection
    {
        $row = DB::table('users')
            ->leftJoin('orders', 'users.id', '=', 'orders.user_id')
            ->select([
                'users.id',
                'users.email',
                'users.name',
                DB::raw('COUNT(orders.id) as order_count'),
                DB::raw('COALESCE(SUM(orders.total), 0) as total_spent'),
                DB::raw('MAX(orders.created_at) as last_order_date'),
            ])
            ->where('users.id', $userId)
            ->groupBy('users.id', 'users.email', 'users.name')
            ->first();
        
        if (!$row) {
            return null;
        }
        
        return UserWithOrdersProjection::fromRaw((array) $row);
    }
}
```

## Event Sourcing with CQRS

For complex domains, combine CQRS with Event Sourcing:

```php
<?php
// app/Domain/Events/OrderPlaced.php

namespace App\Domain\Events;

class OrderPlaced
{
    public function __construct(
        public readonly string $orderId,
        public readonly string $customerId,
        public readonly array $items,
        public readonly \DateTimeImmutable $occurredAt
    ) {}
}
```

```php
<?php
// app/Application/Order/Commands/PlaceOrderCommand.php

namespace App\Application\Order\Commands;

use App\Domain\Entities\Order;
use App\Domain\Events\OrderPlaced;
use App\Domain\Repositories\OrderRepositoryInterface;

class PlaceOrderCommand
{
    public function __construct(
        private OrderRepositoryInterface $orderRepository
    ) {}

    public function execute(array $data): Order
    {
        $order = Order::place(
            $data['customerId'],
            $data['items']
        );
        
        $this->orderRepository->save($order);
        
        // Event will be dispatched by application service
        return $order;
    }
}
```

```php
<?php
// app/Application/Order/Queries/Projections/OrderSummaryProjection.php

namespace App\Application\Order\Queries\Projections;

use Illuminate\Database\Eloquent\Model;

class OrderSummaryProjection extends Model
{
    protected $table = 'order_summaries';
    
    protected $fillable = [
        'order_id',
        'customer_id',
        'status',
        'total',
        'item_count',
        'placed_at',
    ];
    
    protected $casts = [
        'total' => 'decimal:2',
        'placed_at' => 'datetime',
    ];
}
```

## When to Use CQRS

### Use CQRS When

- Complex read queries that differ from write model
- Different teams for read and write sides
- Need for independent scaling
- Event-driven architecture
- Multiple presentation layers (web, mobile, API)

### Don't Use CQRS When

- Simple CRUD operations
- Same model for read/write
- Small team
- Rapid prototyping

## CQRS Benefits

| Benefit | Description |
|---------|-------------|
| **Independent optimization** | Read and write can be optimized separately |
| **Scalability** | Scale reads and writes independently |
| **Flexibility** | Different data models for different views |
| **Testability** | Commands and queries can be tested independently |
| **Performance** | Optimized queries without affecting writes |

## CQRS in Laravel - Best Practices

1. Keep commands and queries in separate directories
2. Use DTOs for input/output
3. Implement validation in commands
4. Create optimized read models for complex queries
5. Consider event sourcing for audit trails
6. Use async processing for heavy write operations