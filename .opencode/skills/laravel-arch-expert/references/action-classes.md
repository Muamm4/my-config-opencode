# Action Classes

## Overview

Action Classes implement single-responsibility use cases. Each Action does exactly one thing and can be reused across HTTP, CLI, and Queue entry points.

## Action Class Structure

```php
<?php
// app/Application/User/Actions/RegisterUser.php

namespace App\Application\User\Actions;

use App\Application\User\DTOs\RegisterUserInput;
use App\Application\User\DTOs\UserDTO;
use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\Repositories\UserRepositoryInterface;

class RegisterUser
{
    private UserRepositoryInterface $userRepository;

    // Dependency Injection via constructor
    public function __construct(
        UserRepositoryInterface $userRepository
    ) {
        $this->userRepository = $userRepository;
    }

    // Execute method with typed input
    public function execute(RegisterUserInput $input): UserDTO
    {
        // 1. Validate business rules
        $this->validateUniqueEmail($input->email);
        
        // 2. Create domain entity
        $user = $this->createUser($input);
        
        // 3. Persist to repository
        $this->userRepository->save($user);
        
        // 4. Return DTO
        return UserDTO::fromEntity($user);
    }

    private function validateUniqueEmail(Email $email): void
    {
        if ($this->userRepository->findByEmail($email)) {
            throw new \InvalidArgumentException(
                "Email already registered: {$email}"
            );
        }
    }

    private function createUser(RegisterUserInput $input): User
    {
        return User::create(
            $input->email,
            $input->name
        );
    }
}
```

## Invokable Action Class

For single-method Actions, use an Invokable controller:

```php
<?php
// app/Application/User/Actions/RegisterUser.php

namespace App\Application\User\Actions;

use App\Application\User\DTOs\RegisterUserInput;
use App\Application\User\DTOs\UserDTO;
use App\Domain\Repositories\UserRepositoryInterface;

class RegisterUser
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function __invoke(RegisterUserInput $input): UserDTO
    {
        // Validate
        $this->validateUniqueEmail($input->email);
        
        // Create
        $user = \App\Domain\Entities\User::create(
            $input->email,
            $input->name
        );
        
        // Persist
        $this->userRepository->save($user);
        
        // Return
        return UserDTO::fromEntity($user);
    }

    private function validateUniqueEmail(\App\Domain\ValueObjects\Email $email): void
    {
        if ($this->userRepository->findByEmail($email)) {
            throw new \InvalidArgumentException(
                "Email already registered"
            );
        }
    }
}
```

## Usage in Controller

```php
<?php
// app/Interfaces/Http/Controllers/UserController.php

namespace App\Interfaces\Http\Controllers;

use App\Http\Requests\RegisterUserRequest;
use App\Application\User\Actions\RegisterUser;
use App\Application\User\DTOs\RegisterUserInput;
use App\Http\Resources\UserResource;

class UserController
{
    public function store(RegisterUserRequest $request)
    {
        // Get validated data
        $input = RegisterUserInput::fromRequest($request);
        
        // Execute Action
        $action = app(RegisterUser::class);
        $userDto = $action($input);
        
        // Return resource
        return new UserResource($userDto);
    }
}
```

## Usage in Queue Job

```php
<?php
// app/Jobs/RegisterUserJob.php

namespace App\Jobs;

use App\Application\User\Actions\RegisterUser;
use App\Application\User\DTOs\RegisterUserInput;

class RegisterUserJob implements \ShouldQueue
{
    public function handle(RegisterUser $action): void
    {
        $input = RegisterUserInput::fromArray($this->data);
        $action($input);
    }
}
```

## Action Naming Convention

| Type | Naming | Example |
|------|--------|---------|
| Create | `[Entity]CreateAction` | `UserCreateAction` |
| Update | `[Entity]UpdateAction` | `UserUpdateAction` |
| Delete | `[Entity]DeleteAction` | `UserDeleteAction` |
| Send | `[Event]SendAction` | `WelcomeEmailSendAction` |
| Process | `[Process]Action` | `PaymentProcessAction` |

## Action vs Service

| Aspect | Action | Service |
|--------|--------|---------|
| **Responsibility** | Single use case | Reusable logic |
| **Methods** | One (`execute` or `__invoke`) | Multiple |
| **Side Effects** | Yes (one complete operation) | Varies |
| **Example** | `RegisterUser` | `DeliveryCalculator` |

**Use Actions when**:
- Orchestrating a complete user workflow
- Need to call from HTTP, Queue, and CLI identically

**Use Services when**:
- Logic/calculation needs to be reused
- No side effects (pure functions)