# DTOs (Data Transfer Objects)

## Overview

DTOs (Data Transfer Objects) provide typed input/output contracts between layers. They replace associative arrays with explicit, type-safe objects.

## Input DTO

Input DTOs validate and encapsulate incoming data.

```php
<?php
// app/Application/User/DTOs/RegisterUserInput.php

namespace App\Application\User\DTOs;

use App\Domain\ValueObjects\Email;
use Illuminate\Foundation\Http\FormRequest;

class RegisterUserInput
{
    public function __construct(
        public readonly Email $email,
        public readonly string $name,
        public readonly ?string $phone = null
    ) {}

    public static function fromArray(array $data): self
    {
        return new self(
            Email::fromString($data['email']),
            $data['name'],
            $data['phone'] ?? null
        );
    }

    public static function fromRequest(FormRequest $request): self
    {
        return self::fromArray($request->validated());
    }

    public function toArray(): array
    {
        return [
            'email' => $this->email->value(),
            'name' => $this->name,
            'phone' => $this->phone,
        ];
    }
}
```

```php
<?php
// app/Application/User/DTOs/UpdateUserInput.php

namespace App\Application\User\DTOs;

use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;

class UpdateUserInput
{
    public function __construct(
        public readonly UserId $userId,
        public readonly ?Email $email = null,
        public readonly ?string $name = null,
        public readonly ?string $phone = null
    ) {}

    public static function fromArray(array $data): self
    {
        return new self(
            UserId::fromString($data['userId']),
            isset($data['email']) ? Email::fromString($data['email']) : null,
            $data['name'] ?? null,
            $data['phone'] ?? null
        );
    }

    public function hasEmail(): bool
    {
        return $this->email !== null;
    }

    public function hasName(): bool
    {
        return $this->name !== null;
    }

    public function hasPhone(): bool
    {
        return $this->phone !== null;
    }
}
```

## Output DTO

Output DTOs represent domain entities for external consumption.

```php
<?php
// app/Application/User/DTOs/UserDTO.php

namespace App\Application\User\DTOs;

use App\Domain\Entities\User;

class UserDTO
{
    public function __construct(
        public readonly string $id,
        public readonly string $email,
        public readonly string $name,
        public readonly bool $isActive,
        public readonly string $createdAt,
        public readonly ?string $phone = null
    ) {}

    public static function fromEntity(User $user): self
    {
        return new self(
            $user->id()->value(),
            $user->email()->value(),
            $user->name(),
            $user->isActive(),
            $user->createdAt()->format('Y-m-d H:i:s'),
            $user->phone()?->value()
        );
    }

    public static function fromArray(array $data): self
    {
        return new self(
            $data['id'],
            $data['email'],
            $data['name'],
            $data['isActive'],
            $data['createdAt'],
            $data['phone'] ?? null
        );
    }

    public function toArray(): array
    {
        return [
            'id' => $this->id,
            'email' => $this->email,
            'name' => $this->name,
            'isActive' => $this->isActive,
            'createdAt' => $this->createdAt,
            'phone' => $this->phone,
        ];
    }

    public function toResource(): array
    {
        return [
            'type' => 'users',
            'id' => $this->id,
            'attributes' => [
                'email' => $this->email,
                'name' => $this->name,
                'isActive' => $this->isActive,
                'createdAt' => $this->createdAt,
            ],
        ];
    }
}
```

## Response DTO with Metadata

```php
<?php
// app/Application/User/DTOs/UserListDTO.php

namespace App\Application\User\DTOs;

use Illuminate\Contracts\Pagination\LengthAwarePaginator;

class UserListDTO
{
    /**
     * @param UserDTO[] $users
     */
    public function __construct(
        public readonly array $users,
        public readonly int $total,
        public readonly int $page,
        public readonly int $perPage,
        public readonly int $lastPage
    ) {}

    public static function fromPaginator(
        LengthAwarePaginator $paginator
    ): self {
        $users = array_map(
            fn($user) => UserDTO::fromEntity($user),
            $paginator->items()
        );

        return new self(
            $users,
            $paginator->total(),
            $paginator->currentPage(),
            $paginator->perPage(),
            $paginator->lastPage()
        );
    }

    public function toArray(): array
    {
        return [
            'data' => array_map(
                fn(UserDTO $user) => $user->toArray(),
                $this->users
            ),
            'meta' => [
                'total' => $this->total,
                'page' => $this->page,
                'perPage' => $this->perPage,
                'lastPage' => $this->lastPage,
            ],
        ];
    }
}
```

## DTO Benefits

| Benefit | Description |
|---------|------------|
| **Type Safety** | Compile-time checking of data shapes |
| **Validation** | Centralized input validation |
| **Refactoring** | Safe changes with IDE support |
| **Documentation** | Self-documenting code |
| **Testing** | Easy mocking of input/output |