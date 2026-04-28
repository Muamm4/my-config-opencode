# Bounded Contexts

## Overview

Bounded Contexts are the fundamental building block of Domain-Driven Design. Each context defines a explicit boundary within which a particular domain model exists, with its own ubiquitous language.

## What is a Bounded Context?

A bounded context is a logical boundary where:
- A specific domain model applies
- A Ubiquitous Language is used consistently
- Certain terms have specific meanings
- Teams can work independently

## Identifying Bounded Contexts

Common patterns for identifying contexts:

| Pattern | Example |
|---------|---------|
| **Natural domain boundaries** | Billing, Shipping, Catalog |
| **Different data ownership** | Customer data vs Product data |
| **Different update frequencies** | Real-time vs Batch processing |
| **Different teams** | Frontend team vs Backend team |

## Laravel Project Structure with Bounded Contexts

```
app/
├── Contexts/
│   ├── Billing/
│   │   ├── Domain/
│   │   │   ├── Entities/
│   │   │   ├── ValueObjects/
│   │   │   ├── Services/
│   │   │   └── Events/
│   │   ├── Application/
│   │   │   ├── Actions/
│   │   │   ├── DTOs/
│   │   │   └── Queries/
│   │   └── Infrastructure/
│   │       ├── Persistence/
│   │       └── Services/
│   │
│   ├── Shipping/
│   │   ├── Domain/
│   │   ├── Application/
│   │   └── Infrastructure/
│   │
│   └── Catalog/
│       ├── Domain/
│       ├── Application/
│       └── Infrastructure/
│
└── Shared/
    ├── Kernel/           # Cross-cutting concerns
    └── Common/           # Shared infrastructure
```

## Context Mapping

Contexts need to communicate. Common patterns:

### Partnership

Two contexts work together, with synchronized development.

```
┌─────────────┐     ┌─────────────┐
│   Billing   │────▶│   Shipping  │
│   Context   │◀────│   Context   │
└─────────────┘     └─────────────┘
```

### Customer-Supplier

One context (supplier) provides services to another (customer).

```
┌─────────────┐     ┌─────────────┐
│   Catalog   │────▶│   Billing   │
│  (Supplier) │     │ (Customer)  │
└─────────────┘     └─────────────┘
```

### Conformist

One context adapts to another's model.

### Anticorruption Layer

One context translates another's model to protect its own.

## Example: E-commerce Bounded Contexts

### Order Context

```php
<?php
// app/Contexts/Order/Domain/Entities/Order.php

namespace App\Contexts\Order\Domain\Entities;

use App\Contexts\Order\Domain\ValueObjects\OrderId;
use App\Contexts\Order\Domain\ValueObjects\OrderStatus;

class Order
{
    private OrderId $id;
    private OrderStatus $status;
    private array $items;
    private \DateTimeImmutable $createdAt;
    
    // ... domain logic specific to orders
}
```

### Billing Context

```php
<?php
// app/Contexts/Billing/Domain/Entities/Invoice.php

namespace App\Contexts\Billing\Domain\Entities;

use App\Contexts\Billing\Domain\ValueObjects\InvoiceId;
use App\Contexts\Billing\Domain\ValueObjects\Money;

class Invoice
{
    private InvoiceId $id;
    private Money $total;
    private string $status;
    
    // ... domain logic specific to billing
}
```

### Integration Between Contexts

```php
<?php
// app/Contexts/Order/Application/Actions/CompleteOrder.php

namespace App\Contexts\Order\Application\Actions;

use App\Contexts\Order\Domain\Entities\Order;
use App\Contexts\Billing\Application\Actions\CreateInvoiceAction;
use App\Contexts\Billing\Application\DTOs\CreateInvoiceInput;

class CompleteOrder
{
    public function __construct(
        private CreateInvoiceAction $createInvoiceAction
    ) {}
    
    public function execute(Order $order): void
    {
        // Order domain logic
        $order->complete();
        
        // Call Billing context via application service
        $invoiceInput = new CreateInvoiceInput(
            $order->id()->value(),
            $order->total()->value(),
            $order->customerId()
        );
        
        $this->createInvoiceAction->execute($invoiceInput);
    }
}
```

## Context Boundaries in Laravel

### When to Split

- Different business rules
- Different teams working on them
- Different scaling requirements
- Different persistence needs

### When NOT to Split

- Simple CRUD operations
- Tightly coupled domains
- Small team (2-3 developers)
- MVP phase

## Anti-Patterns to Avoid

1. **God Context**: Everything in one context
2. **Over-fragmentation**: Too many tiny contexts
3. **Leaky Abstractions**: Contexts knowing too much about each other
4. **Shared Database**: Multiple contexts using the same tables

## Practical Guidelines

1. Start with a single context for small projects
2. Extract contexts when you notice different vocabularies
3. Use an Anticorruption Layer when integrating with legacy systems
4. Keep contexts as small as possible but no smaller
5. Document context boundaries in a context map