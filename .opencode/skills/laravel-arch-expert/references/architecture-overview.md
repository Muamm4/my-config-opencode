# Architecture Overview

## The 4-Layer Architecture

Laravel applications following DDD and Clean Architecture organize code into 4 distinct layers:

```
app/
├── Domain/              # Pure business logic - NO Laravel dependencies
├── Application/         # Use cases, Actions, DTOs - ONLY depends on Domain
├── Infrastructure/      # Eloquent, Repositories, Cache - Implements interfaces
└── Interfaces/         # Controllers, Routes, Console - Entry points
```

## Layer Responsibilities

### 1. Domain Layer (Innermost)

The Domain is the heart of the application. It contains:

- **Entities**: Objects with identity and lifecycle
- **Value Objects**: Immutable objects defined by their values
- **Aggregate Roots**: Entities that coordinate consistency
- **Domain Services**: Business rules spanning multiple entities
- **Domain Events**: Things that happened in the domain
- **Repository Interfaces**: Contracts for data persistence

**Key Principle**: Domain layer has ZERO dependencies on Laravel, Eloquent, or any external framework.

### 2. Application Layer (Orchestration)

The Application layer coordinates the domain:

- **Actions**: Single-purpose use case classes
- **DTOs**: Data Transfer Objects for input/output
- **Application Services**: Coordinate multiple Actions
- **Queries**: Read operations (CQRS)

**Key Principle**: Application layer depends ONLY on Domain interfaces.

### 3. Infrastructure Layer (Technical)

Infrastructure implements the interfaces defined by upper layers:

- **Eloquent Models**: Database persistence
- **Repository Implementations**: Data access logic
- **External Services**: API clients, queue handlers
- **Caching**: Cache implementations
- **Mappers**: Entity ↔ Model transformations

### 4. Interfaces Layer (Entry Points)

The outermost layer handles external communication:

- **HTTP Controllers**: API and web controllers
- **Console Commands**: Artisan commands
- **Queue Jobs**: Async processing
- **Resources**: Response transformers

## Directory Structure Example

```
app/
├── Domain/
│   ├── Entities/
│   │   └── User.php
│   ├── ValueObjects/
│   │   ├── Email.php
│   │   └── UserId.php
│   ├── Events/
│   │   └── UserRegistered.php
│   ├── Services/
│   │   └── UserDomainService.php
│   └── Repositories/
│       └── UserRepositoryInterface.php
│
├── Application/
│   ├── User/
│   │   ├── Actions/
│   │   │   └── RegisterUser.php
│   │   ├── DTOs/
│   │   │   ├── RegisterUserInput.php
│   │   │   └── UserDTO.php
│   │   └── Queries/
│   │       └── GetUserById.php
│   └── Services/
│       └── UserApplicationService.php
│
├── Infrastructure/
│   ├── Persistence/
│   │   ├── Eloquent/
│   │   │   └── UserModel.php
│   │   ├── Mappers/
│   │   │   └── UserMapper.php
│   │   └── Repositories/
│   │       └── EloquentUserRepository.php
│   └── Services/
│       └── MailService.php
│
└── Interfaces/
    └── Http/
        └── Controllers/
            └── UserController.php
```

## When to Use This Architecture

**Use Full DDD When**:
- Project will live for 3+ years
- Complex business domain with many rules
- Team of 5+ developers
- Multiple bounded contexts exist

**Use Simplified Pattern When**:
- Small to medium project
- CRUD-dominated functionality
- Fast MVP required
- Team of 1-3 developers

## Progressive Migration Path

1. **Phase 1**: Extract business logic from Controllers to Actions
2. **Phase 2**: Create DTOs for typed input/output
3. **Phase 3**: Extract Domain entities from Eloquent models
4. **Phase 4**: Create Repository interfaces
5. **Phase 5**: Move to full layer structure