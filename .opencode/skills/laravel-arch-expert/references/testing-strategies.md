# Testing Strategies for Architectural Layers

## Overview

Testing each architectural layer requires different strategies. The testing pyramid for DDD applications in Laravel follows this distribution:

- **Domain Unit Tests** (60-80%): Entities, Value Objects, Domain Services
- **Application Tests** (15-25%): Actions, DTOs, Use Cases
- **Infrastructure Tests** (10-15%): Repositories, External Services
- **Feature Tests** (5-10%): HTTP Endpoints, Critical User Flows

## Domain Layer Testing

Domain tests should be framework-agnostic, fast, and test business invariants.

### Testing Entities

```php
<?php
// tests/Domain/Entities/UserTest.php

namespace Tests\Domain\Entities;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Events\UserRegistered;
use PHPUnit\Framework\TestCase;

class UserTest extends TestCase
{
    public function test_create_user_generates_domain_event(): void
    {
        $email = Email::fromString('test@example.com');
        $name = 'John Doe';
        
        $user = User::create($email, $name);
        
        $events = $user->releaseEvents();
        
        $this->assertCount(1, $events);
        $this->assertInstanceOf(UserRegistered::class, $events[0]);
    }

    public function test_user_name_must_be_at_least_2_characters(): void
    {
        $email = Email::fromString('test@example.com');
        
        $this->expectException(\DomainException::class);
        $this->expectExceptionMessage('Name must be at least 2 characters');
        
        User::create($email, 'A');
    }

    public function test_user_name_must_not_exceed_255_characters(): void
    {
        $email = Email::fromString('test@example.com');
        $name = str_repeat('a', 256);
        
        $this->expectException(\DomainException::class);
        $this->expectExceptionMessage('Name must not exceed 255 characters');
        
        User::create($email, $name);
    }

    public function test_change_email_records_event(): void
    {
        $user = User::create(
            Email::fromString('old@example.com'),
            'John'
        );
        
        $newEmail = Email::fromString('new@example.com');
        $user->changeEmail($newEmail);
        
        $events = $user->releaseEvents();
        
        $this->assertCount(1, $events);
        $this->assertInstanceOf(\App\Domain\Events\UserEmailChanged::class, $events[0]);
    }

    public function test_change_email_to_same_value_does_not_record_event(): void
    {
        $email = Email::fromString('same@example.com');
        $user = User::create($email, 'John');
        
        $user->changeEmail($email);
        
        $events = $user->releaseEvents();
        
        $this->assertCount(0, $events);
    }

    public function test_reconstitute_returns_user_with_correct_state(): void
    {
        $id = UserId::generate();
        $email = Email::fromString('test@example.com');
        $createdAt = new \DateTimeImmutable('2024-01-01 12:00:00');
        
        $user = User::reconstitute(
            $id,
            $email,
            'John Doe',
            true,
            $createdAt
        );
        
        $this->assertTrue($id->equals($user->id()));
        $this->assertTrue($email->equals($user->email()));
        $this->assertEquals('John Doe', $user->name());
        $this->assertTrue($user->isActive());
        $this->assertEquals($createdAt, $user->createdAt());
    }
}
```

### Testing Value Objects

```php
<?php
// tests/Domain/ValueObjects/EmailTest.php

namespace Tests\Domain\ValueObjects;

use App\Domain\ValueObjects\Email;
use PHPUnit\Framework\TestCase;

class EmailTest extends TestCase
{
    public function test_valid_email_creates_value_object(): void
    {
        $email = Email::fromString('test@example.com');
        
        $this->assertEquals('test@example.com', $email->value());
    }

    public function test_email_is_normalized_to_lowercase(): void
    {
        $email = Email::fromString('TEST@Example.COM');
        
        $this->assertEquals('test@example.com', $email->value());
    }

    public function test_email_trims_whitespace(): void
    {
        $email = Email::fromString('  test@example.com  ');
        
        $this->assertEquals('test@example.com', $email->value());
    }

    public function test_invalid_email_throws_exception(): void
    {
        $this->expectException(\InvalidArgumentException::class);
        
        Email::fromString('not-an-email');
    }

    public function test_email_equality_by_value(): void
    {
        $email1 = Email::fromString('test@example.com');
        $email2 = Email::fromString('test@example.com');
        $email3 = Email::fromString('other@example.com');
        
        $this->assertTrue($email1->equals($email2));
        $this->assertFalse($email1->equals($email3));
    }

    public function test_email_can_be_cast_to_string(): void
    {
        $email = Email::fromString('test@example.com');
        
        $this->assertEquals('test@example.com', (string) $email);
    }
}
```

### Testing Domain Services

```php
<?php
// tests/Domain/Services/UserDomainServiceTest.php

namespace Tests\Domain\Services;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Repositories\UserRepositoryInterface;
use App\Domain\Services\UserDomainService;
use PHPUnit\Framework\TestCase;
use Mockery;

class UserDomainServiceTest extends TestCase
{
    protected function tearDown(): void
    {
        Mockery::close();
    }

    public function test_change_user_email_successfully(): void
    {
        $userId = UserId::generate();
        $oldEmail = Email::fromString('old@example.com');
        $newEmail = Email::fromString('new@example.com');
        
        $user = User::reconstitute(
            $userId,
            $oldEmail,
            'John',
            true,
            new \DateTimeImmutable()
        );
        
        $repository = Mockery::mock(UserRepositoryInterface::class);
        $repository->shouldReceive('findById')
            ->with($userId)
            ->andReturn($user);
        $repository->shouldReceive('findByEmail')
            ->with($newEmail)
            ->andReturn(null);
        $repository->shouldReceive('save')
            ->once();
        
        $service = new UserDomainService($repository);
        $result = $service->changeUserEmail($userId, $newEmail);
        
        $this->assertTrue($newEmail->equals($result->email()));
    }

    public function test_change_email_to_existing_email_throws_exception(): void
    {
        $userId = UserId::generate();
        $oldEmail = Email::fromString('old@example.com');
        $newEmail = Email::fromString('new@example.com');
        
        $user = User::reconstitute(
            $userId,
            $oldEmail,
            'John',
            true,
            new \DateTimeImmutable()
        );
        
        $otherUser = User::reconstitute(
            UserId::generate(),
            $newEmail,
            'Jane',
            true,
            new \DateTimeImmutable()
        );
        
        $repository = Mockery::mock(UserRepositoryInterface::class);
        $repository->shouldReceive('findById')
            ->with($userId)
            ->andReturn($user);
        $repository->shouldReceive('findByEmail')
            ->with($newEmail)
            ->andReturn($otherUser);
        
        $service = new UserDomainService($repository);
        
        $this->expectException(\DomainException::class);
        $this->expectExceptionMessage('Email already in use');
        
        $service->changeUserEmail($userId, $newEmail);
    }

    public function test_change_email_for_nonexistent_user_throws_exception(): void
    {
        $userId = UserId::generate();
        $newEmail = Email::fromString('new@example.com');
        
        $repository = Mockery::mock(UserRepositoryInterface::class);
        $repository->shouldReceive('findById')
            ->with($userId)
            ->andReturn(null);
        
        $service = new UserDomainService($repository);
        
        $this->expectException(\DomainException::class);
        $this->expectExceptionMessage('User not found');
        
        $service->changeUserEmail($userId, $newEmail);
    }
}
```

## Application Layer Testing

Application tests verify use cases with mocked repositories.

### Testing Action Classes

```php
<?php
// tests/Application/User/Actions/RegisterUserTest.php

namespace Tests\Application\User\Actions;

use App\Application\User\Actions\RegisterUser;
use App\Application\User\DTOs\RegisterUserInput;
use App\Application\User\DTOs\UserDTO;
use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Repositories\UserRepositoryInterface;
use PHPUnit\Framework\TestCase;
use Mockery;

class RegisterUserTest extends TestCase
{
    private RegisterUser $action;
    private $repository;

    protected function setUp(): void
    {
        parent::setUp();
        
        $this->repository = Mockery::mock(UserRepositoryInterface::class);
        $this->action = new RegisterUser($this->repository);
    }

    protected function tearDown(): void
    {
        Mockery::close();
    }

    public function test_register_user_creates_and_persists_user(): void
    {
        $input = new RegisterUserInput(
            Email::fromString('test@example.com'),
            'John Doe'
        );
        
        $this->repository->shouldReceive('findByEmail')
            ->once()
            ->andReturn(null);
        
        $this->repository->shouldReceive('save')
            ->once()
            ->withArgs(function (User $user) {
                return $user->email()->value() === 'test@example.com'
                    && $user->name() === 'John Doe';
            });
        
        $result = $this->action->execute($input);
        
        $this->assertInstanceOf(UserDTO::class, $result);
        $this->assertEquals('test@example.com', $result->email);
        $this->assertEquals('John Doe', $result->name);
    }

    public function test_register_user_with_existing_email_throws_exception(): void
    {
        $input = new RegisterUserInput(
            Email::fromString('existing@example.com'),
            'John Doe'
        );
        
        $existingUser = User::reconstitute(
            UserId::generate(),
            Email::fromString('existing@example.com'),
            'Jane',
            true,
            new \DateTimeImmutable()
        );
        
        $this->repository->shouldReceive('findByEmail')
            ->once()
            ->with(Email::fromString('existing@example.com'))
            ->andReturn($existingUser);
        
        $this->expectException(\InvalidArgumentException::class);
        $this->expectExceptionMessage('Email already registered');
        
        $this->action->execute($input);
    }
}
```

### Testing DTOs

```php
<?php
// tests/Application/User/DTOs/RegisterUserInputTest.php

namespace Tests\Application\User\DTOs;

use App\Application\User\DTOs\RegisterUserInput;
use App\Domain\ValueObjects\Email;
use PHPUnit\Framework\TestCase;

class RegisterUserInputTest extends TestCase
{
    public function test_from_array_creates_dto(): void
    {
        $data = [
            'email' => 'test@example.com',
            'name' => 'John Doe',
            'phone' => '+1234567890',
        ];
        
        $input = RegisterUserInput::fromArray($data);
        
        $this->assertInstanceOf(Email::class, $input->email);
        $this->assertEquals('test@example.com', $input->email->value());
        $this->assertEquals('John Doe', $input->name);
        $this->assertEquals('+1234567890', $input->phone);
    }

    public function test_from_array_with_optional_phone(): void
    {
        $data = [
            'email' => 'test@example.com',
            'name' => 'John Doe',
        ];
        
        $input = RegisterUserInput::fromArray($data);
        
        $this->assertNull($input->phone);
    }

    public function test_to_array_returns_correct_structure(): void
    {
        $input = new RegisterUserInput(
            Email::fromString('test@example.com'),
            'John Doe',
            '+1234567890'
        );
        
        $result = $input->toArray();
        
        $this->assertEquals([
            'email' => 'test@example.com',
            'name' => 'John Doe',
            'phone' => '+1234567890',
        ], $result);
    }
}
```

## Infrastructure Layer Testing

Infrastructure tests use real implementations with test databases.

### Testing Repository Implementation

```php
<?php
// tests/Infrastructure/Persistence/Repositories/EloquentUserRepositoryTest.php

namespace Tests\Infrastructure\Persistence\Repositories;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;
use App\Infrastructure\Persistence\Repositories\EloquentUserRepository;
use App\Infrastructure\Persistence\Mappers\UserMapper;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class EloquentUserRepositoryTest extends TestCase
{
    use RefreshDatabase;

    private EloquentUserRepository $repository;
    private UserMapper $mapper;

    protected function setUp(): void
    {
        parent::setUp();
        
        $this->mapper = new UserMapper();
        $this->repository = new EloquentUserRepository($this->mapper);
    }

    public function test_find_by_id_returns_user(): void
    {
        $userModel = UserModel::create([
            'id' => UserId::generate()->value(),
            'email' => 'test@example.com',
            'name' => 'John Doe',
            'is_active' => true,
        ]);
        
        $user = $this->repository->findById(
            UserId::fromString($userModel->id)
        );
        
        $this->assertNotNull($user);
        $this->assertEquals('test@example.com', $user->email()->value());
        $this->assertEquals('John Doe', $user->name());
    }

    public function test_find_by_id_returns_null_for_nonexistent(): void
    {
        $user = $this->repository->findById(
            UserId::generate()
        );
        
        $this->assertNull($user);
    }

    public function test_save_persists_user(): void
    {
        $user = User::create(
            Email::fromString('new@example.com'),
            'Jane Doe'
        );
        
        $this->repository->save($user);
        
        $this->assertDatabaseHas('users', [
            'email' => 'new@example.com',
            'name' => 'Jane Doe',
        ]);
    }

    public function test_delete_removes_user(): void
    {
        $userModel = UserModel::create([
            'id' => UserId::generate()->value(),
            'email' => 'test@example.com',
            'name' => 'John',
            'is_active' => true,
        ]);
        
        $this->repository->delete(UserId::fromString($userModel->id));
        
        $this->assertDatabaseMissing('users', [
            'id' => $userModel->id,
        ]);
    }

    public function test_find_by_email_returns_user(): void
    {
        UserModel::create([
            'id' => UserId::generate()->value(),
            'email' => 'test@example.com',
            'name' => 'John',
            'is_active' => true,
        ]);
        
        $user = $this->repository->findByEmail(
            Email::fromString('test@example.com')
        );
        
        $this->assertNotNull($user);
        $this->assertEquals('test@example.com', $user->email()->value());
    }
}
```

### Testing External Service Clients

```php
<?php
// tests/Infrastructure/Services/PaymentGateway/StripePaymentGatewayTest.php

namespace Tests\Infrastructure\Services\PaymentGateway;

use App\Infrastructure\Services\PaymentGateway\StripePaymentGateway;
use Illuminate\Support\Facades\Http;
use Tests\TestCase;

class StripePaymentGatewayTest extends TestCase
{
    public function test_charge_successful_payment(): void
    {
        Http::fake([
            'api.stripe.com/v1/charges' => Http::response([
                'id' => 'ch_123',
                'amount' => 5000,
                'status' => 'succeeded',
            ], 200),
        ]);
        
        $gateway = new StripePaymentGateway('sk_test_xxx');
        
        $result = $gateway->charge([
            'amount' => 5000,
            'currency' => 'usd',
            'token' => 'tok_visa',
            'description' => 'Test payment',
        ]);
        
        $this->assertEquals('ch_123', $result['id']);
        $this->assertEquals('succeeded', $result['status']);
    }

    public function test_charge_failed_payment_throws_exception(): void
    {
        Http::fake([
            'api.stripe.com/v1/charges' => Http::response([
                'error' => [
                    'message' => 'Your card was declined',
                ],
            ], 402),
        ]);
        
        $gateway = new StripePaymentGateway('sk_test_xxx');
        
        $this->expectException(\RuntimeException::class);
        $this->expectExceptionMessage('Payment failed');
        
        $gateway->charge([
            'amount' => 5000,
            'currency' => 'usd',
            'token' => 'tok_chargeDeclined',
        ]);
    }

    public function test_refund_returns_refund_object(): void
    {
        Http::fake([
            'api.stripe.com/v1/refunds' => Http::response([
                'id' => 're_123',
                'status' => 'succeeded',
                'charge' => 'ch_123',
            ], 200),
        ]);
        
        $gateway = new StripePaymentGateway('sk_test_xxx');
        
        $result = $gateway->refund('ch_123');
        
        $this->assertEquals('re_123', $result['id']);
        $this->assertEquals('succeeded', $result['status']);
    }
}
```

## Feature/Integration Testing

Feature tests verify complete HTTP request flows.

### Testing HTTP Endpoints

```php
<?php
// tests/Feature/UserRegistrationTest.php

namespace Tests\Feature;

use App\Domain\ValueObjects\Email;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserRegistrationTest extends TestCase
{
    use RefreshDatabase;

    public function test_user_can_register_with_valid_data(): void
    {
        $response = $this->postJson('/api/users', [
            'email' => 'newuser@example.com',
            'name' => 'John Doe',
            'phone' => '+1234567890',
        ]);
        
        $response->assertStatus(201)
            ->assertJsonStructure([
                'data' => [
                    'id',
                    'email',
                    'name',
                    'isActive',
                    'createdAt',
                ],
            ]);
        
        $this->assertDatabaseHas('users', [
            'email' => 'newuser@example.com',
            'name' => 'John Doe',
        ]);
    }

    public function test_registration_fails_with_invalid_email(): void
    {
        $response = $this->postJson('/api/users', [
            'email' => 'not-an-email',
            'name' => 'John Doe',
        ]);
        
        $response->assertStatus(422)
            ->assertJsonValidationErrors(['email']);
    }

    public function test_registration_fails_with_duplicate_email(): void
    {
        UserModel::create([
            'id' => \Ramsey\Uuid\Uuid::uuid4()->toString(),
            'email' => 'existing@example.com',
            'name' => 'Jane',
            'is_active' => true,
        ]);
        
        $response = $this->postJson('/api/users', [
            'email' => 'existing@example.com',
            'name' => 'John Doe',
        ]);
        
        $response->assertStatus(422)
            ->assertJsonValidationErrors(['email']);
    }

    public function test_registration_fails_without_required_fields(): void
    {
        $response = $this->postJson('/api/users', []);
        
        $response->assertStatus(422)
            ->assertJsonValidationErrors(['email', 'name']);
    }

    public function test_name_must_meet_length_requirements(): void
    {
        $response = $this->postJson('/api/users', [
            'email' => 'test@example.com',
            'name' => 'A', // Too short
        ]);
        
        $response->assertStatus(422)
            ->assertJsonValidationErrors(['name']);
    }
}
```

### Testing User Workflows

```php
<?php
// tests/Feature/UserManagementWorkflowTest.php

namespace Tests\Feature;

use App\Domain\ValueObjects\Email;
use App\Infrastructure\Persistence\Eloquent\Models\UserModel;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserManagementWorkflowTest extends TestCase
{
    use RefreshDatabase;

    public function test_complete_user_lifecycle(): void
    {
        // 1. Register user
        $registerResponse = $this->postJson('/api/users', [
            'email' => 'lifecycle@example.com',
            'name' => 'Lifecycle User',
        ]);
        
        $userId = $registerResponse->json('data.id');
        
        // 2. Get user
        $getResponse = $this->getJson("/api/users/{$userId}");
        
        $getResponse->assertStatus(200)
            ->assertJsonPath('data.email', 'lifecycle@example.com');
        
        // 3. Update user
        $updateResponse = $this->putJson("/api/users/{$userId}", [
            'name' => 'Updated Name',
        ]);
        
        $updateResponse->assertStatus(200)
            ->assertJsonPath('data.name', 'Updated Name');
        
        // 4. Deactivate user
        $deactivateResponse = $this->deleteJson("/api/users/{$userId}");
        
        $deactivateResponse->assertStatus(204);
        
        // 5. Verify deactivated
        $getAfterDeactivate = $this->getJson("/api/users/{$userId}");
        $getAfterDeactivate->assertStatus(404);
    }

    public function test_user_cannot_update_another_user(): void
    {
        $user1 = UserModel::create([
            'id' => \Ramsey\Uuid\Uuid::uuid4()->toString(),
            'email' => 'user1@example.com',
            'name' => 'User One',
            'is_active' => true,
        ]);
        
        $user2 = UserModel::create([
            'id' => \Ramsey\Uuid\Uuid::uuid4()->toString(),
            'email' => 'user2@example.com',
            'name' => 'User Two',
            'is_active' => true,
        ]);
        
        $response = $this->putJson("/api/users/{$user2->id}", [
            'name' => 'Hacked Name',
        ]);
        
        // With proper auth, this should return 403
        $response->assertStatus(403);
    }
}
```

## Testing Best Practices

### Use DatabaseTransactions for Speed

```php
<?php
// phpunit.xml or TestCase

use Illuminate\Foundation\Testing\RefreshDatabase;

// SLOW - drops and recreates DB each test
// use RefreshDatabase;

// FAST - wraps each test in transaction
use Illuminate\Foundation\Testing\DatabaseTransactions;

class UserTest extends TestCase
{
    use DatabaseTransactions;
    
    // Tests run 10-50x faster
}
```

### Use HTTP Fakes for External APIs

```php
public function test_payment_flow(): void
{
    // Fake external API calls
    Http::fake([
        'api.stripe.com/*' => Http::response([
            'id' => 'ch_test',
            'status' => 'succeeded',
        ], 200),
    ]);
    
    // Test runs without real HTTP calls
    $response = $this->post('/checkout', [...]);
}
```

### Test Naming Convention

```php
public function test_description_of_behavior(): void
{
    // AAA Pattern
    // Arrange - set up test data
    // Act - execute the action
    // Assert - verify the outcome
}
```

### What to Test by Layer

| Layer | Test | Skip |
|-------|------|------|
| **Domain** | Business invariants, validation rules, entity behavior | Getters/setters, framework internals |
| **Application** | Action logic, DTO transformation, use case orchestration | Simple property access |
| **Infrastructure** | Repository queries, external service integration | Eloquent internals |
| **HTTP** | Request/response, auth, validation | Framework behavior |

### Test Coverage Guidelines

- **Domain**: 80%+ coverage for entities and value objects
- **Application**: Cover all use cases and edge cases
- **Infrastructure**: Cover all repository methods and external integrations
- **HTTP**: Cover happy paths, error paths, and authorization

## Parallel Testing

Laravel 12+ supports parallel test execution:

```bash
# Run tests in parallel
php artisan test --parallel

# Specify number of processes
php artisan test --parallel --processes=4
```

Ensure tests are independent and don't share state when using parallel execution.