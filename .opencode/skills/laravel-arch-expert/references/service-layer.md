# Service Layer

## Overview

Service Layer contains reusable business logic that doesn't belong to a single entity. Services encapsulate operations that are used across multiple Actions.

## Application Service vs Domain Service

| Aspect | Domain Service | Application Service |
|--------|--------------|-----------------|
| **Location** | Domain layer | Application layer |
| **Dependencies** | Domain interfaces only | Domain + Application |
| **Purpose** | Business rules | Orchestration |

## Domain Service Example

```php
<?php
// app/Domain/Services/PaymentDomainService.php

namespace App\Domain\Services;

use App\Domain\Entities\Order;
use App\Domain\ValueObjects\Money;
use App\Domain\Repositories\OrderRepositoryInterface;

class PaymentDomainService
{
    public function __construct(
        private OrderRepositoryInterface $orderRepository
    ) {}

    public function calculateOrderTotal(string $orderId): Money
    {
        $order = $this->orderRepository->findById($orderId);
        
        if (!$order) {
            throw new \DomainException("Order not found: {$orderId}");
        }
        
        $subtotal = $order->items()->reduce(
            fn(Money $sum, $item) => $sum->add($item->price()->multiply($item->quantity())),
            Money::zero()
        );
        
        $shipping = $this->calculateShipping($order);
        $tax = $subtotal->multiply(0.1); // 10% tax
        
        return $subtotal->add($shipping)->add($tax);
    }

    private function calculateShipping(Order $order): Money
    {
        $weight = $order->items()->sum(fn($item) => $item->weight());
        
        if ($weight->value() > 1000) {
            return new Money(0); // Free shipping over 1kg
        }
        
        return new Money(500); // $5.00 flat shipping
    }
}
```

## Application Service Example

```php
<?php
// app/Application/Services/OrderApplicationService.php

namespace App\Application\Services;

use App\Application\Order\Actions\CreateOrderAction;
use App\Application\Order\Actions\ProcessPaymentAction;
use App\Application\Order\Actions\ShipOrderAction;
use App\Application\Order\DTOs\OrderDTO;
use App\Application\Order\DTOs\CreateOrderInput;
use App\Domain\Events\OrderCreated;
use Illuminate\Support\Facades\Event;

class OrderApplicationService
{
    public function __construct(
        private CreateOrderAction $createOrderAction,
        private ProcessPaymentAction $processPaymentAction,
        private ShipOrderAction $shipOrderAction
    ) {}

    public function createOrder(array $data): OrderDTO
    {
        $input = CreateOrderInput::fromArray($data);
        $result = $this->createOrderAction->execute($input);
        
        // Dispatch events
        Event::dispatch(new OrderCreated($result->orderId));
        
        return $result;
    }

    public function processWorkflow(string $orderId): OrderDTO
    {
        // 1. Create order
        $order = $this->createOrder(['orderId' => $orderId]);
        
        // 2. Process payment
        $this->processPaymentAction->execute($orderId);
        
        // 3. Ship order
        $this->shipOrderAction->execute($orderId);
        
        return $order;
    }
}
```

## Service Naming Convention

| Type | Naming | Example |
|------|-------|---------|
| Domain | `[Domain]DomainService` | `PaymentDomainService` |
| Application | `[Domain]ApplicationService` | `OrderApplicationService` |
| Calculation | `[Calculation]Service` | `DeliveryCalculator` |
| Validation | `[Validator]Service` | `OrderValidator` |
| Transformation | `[Transformer]Service` | `OrderTransformer` |