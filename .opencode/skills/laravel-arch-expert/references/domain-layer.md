# Domain Layer (DDD)

## Overview

The Domain layer contains pure business logic. It has **ZERO dependencies** on Laravel, Eloquent, or any external framework. This layer is framework-agnostic and can be reused in any PHP application.

## Entity Pattern

An Entity is an object with identity that persists over time. Unlike Value Objects, entities are distinguished by their ID, not their attributes.

```php
<?php
// app/Domain/Entities/User.php

namespace App\Domain\Entities;

use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Events\UserRegistered;
use App\Domain\Events\UserEmailChanged;

class User
{
    private UserId $id;
    private Email $email;
    private string $name;
    private bool $isActive;
    private \DateTimeImmutable $createdAt;

    private function __construct(
        UserId $id,
        Email $email,
        string $name,
        bool $isActive,
        \DateTimeImmutable $createdAt
    ) {
        $this->id = $id;
        $this->email = $email;
        $this->name = $name;
        $this->isActive = $isActive;
        $this->createdAt = $createdAt;
    }

    // Factory method - the only way to create a User
    public static function create(
        Email $email,
        string $name
    ): self {
        self::validateName($name);
        
        $user = new self(
            UserId::generate(),
            $email,
            $name,
            true,  // isActive
            new \DateTimeImmutable()
        );
        
        // Domain event - user was registered
        $user->recordThat(new UserRegistered($user));
        
        return $user;
    }

    // Reconstitution from database
    public static function reconstitute(
        UserId $id,
        Email $email,
        string $name,
        bool $isActive,
        \DateTimeImmutable $createdAt
    ): self {
        return new self($id, $email, $name, $isActive, $createdAt);
    }

    // Business methods
    public function changeEmail(Email $newEmail): void
    {
        if ($newEmail->equals($this->email)) {
            return; // No change needed
        }
        
        $this->email = $newEmail;
        $this->recordThat(new UserEmailChanged($this->id, $newEmail));
    }

    public function deactivate(): void
    {
        $this->isActive = false;
    }

    public function activate(): void
    {
        $this->isActive = true;
    }

    // Getters
    public function id(): UserId
    {
        return $this->id;
    }

    public function email(): Email
    {
        return $this->email;
    }

    public function name(): string
    {
        return $this->name;
    }

    public function isActive(): bool
    {
        return $this->isActive;
    }

    public function createdAt(): \DateTimeImmutable
    {
        return $this->createdAt;
    }

    // Validation - private business rule
    private static function validateName(string $name): void
    {
        if (strlen($name) < 2) {
            throw new \DomainException('Name must be at least 2 characters');
        }
        
        if (strlen($name) > 255) {
            throw new \DomainException('Name must not exceed 255 characters');
        }
    }

    // Domain Events handling
    private array $domainEvents = [];

    protected function recordThat(object $event): void
    {
        $this->domainEvents[] = $event;
    }

    public function releaseEvents(): array
    {
        $released = $this->domainEvents;
        $this->domainEvents = [];
        return $released;
    }
}
```

## Value Object Pattern

Value Objects are immutable objects defined by their attributes, not by identity. They compare by value, not reference.

```php
<?php
// app/Domain/ValueObjects/Email.php

namespace App\Domain\ValueObjects;

use InvalidArgumentException;

class Email
{
    private string $value;

    private function __construct(string $value)
    {
        $this->value = $value;
    }

    public static function fromString(string $email): self
    {
        $email = trim($email);
        
        if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidArgumentException(
                "Invalid email format: {$email}"
            );
        }
        
        return new self(strtolower($email));
    }

    public function value(): string
    {
        return $this->value;
    }

    public function equals(self $other): bool
    {
        return $this->value === $other->value;
    }

    // Required for array_unique, array_diff, etc.
    public function __toString(): string
    {
        return $this->value;
    }
}
```

```php
<?php
// app/Domain/ValueObjects/UserId.php

namespace App\Domain\ValueObjects;

use InvalidArgumentException;

class UserId
{
    private string $value;

    private function __construct(string $value)
    {
        $this->value = $value;
    }

    public static function generate(): self
    {
        return new self(\Ramsey\Uuid\Uuid::uuid4()->toString());
    }

    public static function fromString(string $id): self
    {
        if (!\Ramsey\Uuid\Uuid::isValid($id)) {
            throw new InvalidArgumentException("Invalid UUID: {$id}");
        }
        
        return new self($id);
    }

    public function value(): string
    {
        return $this->value;
    }

    public function equals(self $other): bool
    {
        return $this->value === $other->value;
    }

    public function __toString(): string
    {
        return $this->value;
    }
}
```

## Domain Events

Domain Events represent something significant that happened in the domain.

```php
<?php
// app/Domain/Events/UserRegistered.php

namespace App\Domain\Events;

use App\Domain\ValueObjects\UserId;

class UserRegistered
{
    public function __construct(
        public readonly UserId $userId,
        public readonly string $email,
        public readonly \DateTimeImmutable $occurredAt
    ) {
        $this->occurredAt = $occurredAt;
    }

    public static function forUser(UserId $userId, string $email): self
    {
        return new self(
            $userId,
            $email,
            new \DateTimeImmutable()
        );
    }
}
```

## Repository Interface (Domain)

Repository interfaces belong in the Domain layer. The implementation belongs in Infrastructure.

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
    
    public function save(User $user): void;
    
    public function delete(UserId $id): void;
    
    public function all(): iterable;
}
```

## Domain Service

Domain Services implement business logic that doesn't belong to a single entity.

```php
<?php
// app/Domain/Services/UserDomainService.php

namespace App\Domain\Services;

use App\Domain\Entities\User;
use App\Domain\Repositories\UserRepositoryInterface;
use App\Domain\ValueObjects\Email;

class UserDomainService
{
    public function __construct(
        private UserRepositoryInterface $userRepository
    ) {}

    public function changeUserEmail(
        UserId $userId,
        Email $newEmail
    ): User {
        $user = $this->userRepository->findById($userId);
        
        if (!$user) {
            throw new \DomainException("User not found: {$userId}");
        }
        
        // Check business rule - email must be unique
        $existingUser = $this->userRepository->findByEmail($newEmail);
        
        if ($existingUser && !$existingUser->id()->equals($userId)) {
            throw new \DomainException(
                "Email already in use: {$newEmail}"
            );
        }
        
        $user->changeEmail($newEmail);
        $this->userRepository->save($user);
        
        return $user;
    }
}
```