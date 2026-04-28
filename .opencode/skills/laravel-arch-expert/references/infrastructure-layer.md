# Infrastructure Layer

## Overview

The Infrastructure layer contains all technical implementations. It depends on all other layers and implements the interfaces defined by higher layers.

## Eloquent Model

```php
<?php
// app/Infrastructure/Persistence/Eloquent/Models/UserModel.php

namespace App\Infrastructure\Persistence\Eloquent\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;

class UserModel extends Model
{
    protected $table = 'users';
    
    protected $keyType = 'string';
    
    public $incrementing = false;
    
    protected $fillable = [
        'id',
        'email',
        'name',
        'phone',
        'is_active',
    ];
    
    protected $casts = [
        'is_active' => 'boolean',
        'created_at' => 'datetime',
        'updated_at' => 'datetime',
    ];

    public function posts(): HasMany
    {
        return $this->hasMany(PostModel::class, 'user_id');
    }
}
```

## Cache Implementation

```php
<?php
// app/Infrastructure/Services/CachedUserRepository.php

namespace App\Infrastructure\Services;

use App\Domain\Entities\User;
use App\Domain\ValueObjects\Email;
use App\Domain\ValueObjects\UserId;
use App\Domain\Repositories\UserRepositoryInterface;
use Illuminate\Support\Facades\Cache;

class CachedUserRepository implements UserRepositoryInterface
{
    private const CACHE_TTL = 3600; // 1 hour

    public function __construct(
        private UserRepositoryInterface $delegate
    ) {}

    public function findById(UserId $id): ?User
    {
        $key = "user:{$id->value()}";
        
        return Cache::remember($key, self::CACHE_TTL, function() use ($id) {
            return $this->delegate->findById($id);
        });
    }

    public function findByEmail(Email $email): ?User
    {
        $key = "user:email:{$email->value()}";
        
        return Cache::remember($key, self::CACHE_TTL, function() use ($email) {
            return $this->delegate->findByEmail($email);
        });
    }

    public function save(User $user): void
    {
        $this->delegate->save($user);
        $this->clearCache($user->id());
    }

    public function delete(UserId $id): void
    {
        $this->delegate->delete($id);
        $this->clearCache($id);
    }

    private function clearCache(UserId $id): void
    {
        Cache::forget("user:{$id->value()}");
    }

    // ... delegate other methods
}
```

## Event Dispatcher Implementation

```php
<?php
// app/Infrastructure/Events/LaravelEventDispatcher.php

namespace App\Infrastructure\Events;

use App\Domain\Events\DomainEvent;
use App\Domain\Events\EventDispatcherInterface;
use Illuminate\Support\Facades\Event;

class LaravelEventDispatcher implements EventDispatcherInterface
{
    public function dispatch(DomainEvent $event): void
    {
        Event::dispatch($event);
    }

    public function listen(
        string $eventClass,
        callable $handler
    ): void {
        Event::listen($eventClass, $handler);
    }
}
```

## External API Client

```php
<?php
// app/Infrastructure/Services/PaymentGateway/StripePaymentGateway.php

namespace App\Infrastructure\Services\PaymentGateway;

use App\Domain\Services\PaymentGatewayInterface;
use App\Domain\ValueObjects\Money;
use Illuminate\Support\Facades\Http;

class StripePaymentGateway implements PaymentGatewayInterface
{
    public function __construct(
        private string $apiKey
    ) {}

    public function charge(array $data): array
    {
        $response = Http::withToken($this->apiKey)
            ->post('https://api.stripe.com/v1/charges', [
                'amount' => $data['amount'],
                'currency' => $data['currency'],
                'source' => $data['token'],
                'description' => $data['description'] ?? null,
            ]);
        
        if ($response->failed()) {
            throw new \RuntimeException(
                'Payment failed: ' . $response->body()
            );
        }
        
        return $response->json();
    }

    public function refund(string $chargeId): array
    {
        $response = Http::withToken($this->apiKey)
            ->post('https://api.stripe.com/v1/refunds', [
                'charge' => $chargeId,
            ]);
        
        return $response->json();
    }
}
```