# Application Layer

## Overview

The Application layer orchestrates the domain. It contains use cases (Actions), DTOs, and Application Services. This layer depends ONLY on the Domain layer - never on Infrastructure or Laravel directly.

## Application Service Pattern

Application Services coordinate multiple Actions and handle cross-cutting concerns like transactions and events.

```php
<?php
// app/Application/Services/UserApplicationService.php

namespace App\Application\Services;

use App\Domain\Repositories\UserRepositoryInterface;
use App\Domain\Events\UserRegistered;
use Illuminate\Support\Facades\Event;

class UserApplicationService
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function register(array $data): array
    {
        // Create Action - single responsibility
        $action = new \App\Application\User\Actions\RegisterUser(
            $this->userRepository
        );
        
        // Execute with DTO
        $input = \App\Application\User\DTOs\RegisterUserInput::fromArray($data);
        $result = $action($input);
        
        // Dispatch domain events
        foreach ($result->user->releaseEvents() as $event) {
            if ($event instanceof UserRegistered) {
                Event::dispatch($event);
            }
        }
        
        return $result->toArray();
    }
}
```

## Use Case (CQRS Pattern)

Separate Commands (write) from Queries (read).

```php
<?php
// app/Application/User/Commands/CreateUser.php

namespace App\Application\User\Commands;

use App\Application\User\DTOs\CreateUserInput;
use App\Application\User\DTOs\UserDTO;
use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\Repositories\UserRepositoryInterface;

class CreateUser
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function execute(CreateUserInput $input): UserDTO
    {
        // Check if email exists
        $existingUser = $this->userRepository->findByEmail($input->email);
        
        if ($existingUser) {
            throw new \InvalidArgumentException(
                'Email already registered'
            );
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

```php
<?php
// app/Application/User/Queries/GetUserById.php

namespace App\Application\User\Queries;

use App\Application\User\DTOs\UserDTO;
use App\Domain\ValueObjects\UserId;
use App\Domain\Repositories\UserRepositoryInterface;

class GetUserById
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function execute(string $id): ?UserDTO
    {
        $userId = UserId::fromString($id);
        $user = $this->userRepository->findById($userId);
        
        if (!$user) {
            return null;
        }
        
        return UserDTO::fromEntity($user);
    }
}
```