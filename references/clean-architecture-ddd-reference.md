# TypeScript Clean Architecture & DDD Reference

> **For AI Agents:** Treat this as ground truth when generating, refactoring, or reviewing specs, plans, or code.

---

## Folder Structure

Code lives under `apps/src/` and is grouped by **Bounded Context** (e.g., `ordering`, `billing`). Each context follows a strict 4-layer Clean Architecture.

```
/src/
└── [context-name]/
    ├── presenter/               # Layer 1 — Delivery (Input/Output)
    │   ├── controllers/         # Handle requests, call use cases, format responses
    │   ├── routes/              # Map URLs to controllers
    │   ├── dtos/                # Define and validate incoming data shapes
    │   └── middlewares/         # Pre-request checks (auth, logging)
    │
    ├── application/             # Layer 2 — Orchestration (The Brain)
    │   ├── use-cases/           # Actions a user can perform (e.g., PayOrder)
    │   └── interfaces/          # Contracts for outside tools (e.g., IOrderRepository)
    │
    ├── domain/                  # Layer 3 — Core Business Logic (The Truth)
    │   ├── entities/            # Business objects with behaviour + validation
    │   └── models/              # Pure types, schemas, and Value Objects
    │
    └── adapters/                # Layer 4 — Infrastructure (The Tools)
        ├── repository/          # Real database implementations (Prisma, TypeORM)
        └── external-service/    # External API clients (Stripe, SendGrid)
```

---

## Core Rules

### 1. Dependency Rule
Dependencies must **only point inward**. Inner layers cannot know anything about outer layers.

```
[Presenter / Adapters] ──> [Application] ──> [Domain]
```

| Layer | Can import from |
|---|---|
| Presenter & Adapters | Application, Domain |
| Application | Domain only |
| Domain | **Nothing** — no external imports |

### 2. Bounded Context Rule
- Do **not** share domain models or database transactions across contexts.
- Cross-context communication must go through **public application interfaces or events only**.

---

## Layer Responsibilities & Boilerplate

### Domain — Pure Business Logic

Contains only data structures and business rules. Zero framework dependencies.

```typescript
// domain/models/order.model.ts
export type OrderStatus = 'PENDING' | 'PAID';

export interface OrderProps {
  id: string;
  totalAmount: number;
  status: OrderStatus;
}
```

```typescript
// domain/entities/order.entity.ts
import { OrderProps } from '../models/order.model';

export class Order {
  private props: OrderProps;

  constructor(props: OrderProps) {
    if (props.totalAmount < 0) throw new Error('Amount cannot be negative');
    this.props = props;
  }

  public markAsPaid(): void {
    this.props.status = 'PAID';
  }

  public toDTO(): OrderProps {
    return { ...this.props };
  }
}
```

**Value Objects** represent primitives that carry business rules. They are immutable and compared by value, not identity.

```typescript
// domain/models/money.value-object.ts
export class Money {
  constructor(
    readonly amount: number,
    readonly currency: string,
  ) {
    if (amount < 0) throw new Error('Amount cannot be negative');
  }

  equals(other: Money): boolean {
    return this.amount === other.amount && this.currency === other.currency;
  }
}
```

### Application — Workflows & Interfaces

Defines *what the system can do* and *the contracts needed to do it*.

**Testing note:** Because Use Cases depend only on interfaces, you can test them by passing a simple in-memory implementation — no database or framework setup required.

```typescript
// application/interfaces/order-repository.interface.ts
import { Order } from '../../domain/entities/order.entity';

export interface IOrderRepository {
  save(order: Order): Promise<void>;
  findById(id: string): Promise<Order | null>;
}
```

```typescript
// application/use-cases/pay-order.usecase.ts
import { IOrderRepository } from '../interfaces/order-repository.interface';

export class PayOrderUseCase {
  constructor(private orderRepository: IOrderRepository) {}

  async execute(orderId: string): Promise<void> {
    const order = await this.orderRepository.findById(orderId);
    if (!order) throw new Error('Order not found');

    order.markAsPaid();
    await this.orderRepository.save(order);
  }
}
```

### Adapters — Infrastructure Implementations

Implements the interfaces defined by the Application layer.

```typescript
// adapters/repository/order.prisma.repository.ts
import { IOrderRepository } from '../../application/interfaces/order-repository.interface';
import { Order } from '../../domain/entities/order.entity';

export class OrderPrismaRepository implements IOrderRepository {
  async findById(id: string): Promise<Order | null> {
    // Database logic here
    return null;
  }

  async save(order: Order): Promise<void> {
    // Save to database
  }
}
```

### Presenter — Delivery

Handles external requests and validates inputs via DTOs.

```typescript
// presenter/dtos/pay-order.dto.ts
export interface PayOrderDTO {
  orderId: string;
}
```

```typescript
// presenter/controllers/order.controller.ts
import { Request, Response } from 'express';
import { PayOrderUseCase } from '../../application/use-cases/pay-order.usecase';
import { PayOrderDTO } from '../dtos/pay-order.dto';

export class OrderController {
  constructor(private payOrderUseCase: PayOrderUseCase) {}

  async handlePayOrder(req: Request, res: Response): Promise<void> {
    try {
      const dto: PayOrderDTO = req.body;
      await this.payOrderUseCase.execute(dto.orderId);
      res.status(200).json({ message: 'Success' });
    } catch (error) {
      const message = error instanceof Error ? error.message : 'Unknown error';
      res.status(400).json({ error: message });
    }
  }
}
```

---

## Composition Root (Wiring)

All layers are wired together in a single entry point — typically `main.ts` or a dedicated DI module. **This is the only place** where concrete implementations are instantiated.

```typescript
// main.ts
import { OrderPrismaRepository } from './adapters/repository/order.prisma.repository';
import { PayOrderUseCase } from './application/use-cases/pay-order.usecase';
import { OrderController } from './presenter/controllers/order.controller';

const repository = new OrderPrismaRepository();
const useCase = new PayOrderUseCase(repository);
const controller = new OrderController(useCase);
```

This is why Clean Architecture is testable: swap `OrderPrismaRepository` for an in-memory stub and every layer above works identically.

---

## AI Code Generation Checklist

- [ ] **Strict dependency flow** — never import `adapters` or `presenter` into `domain` or `application`
- [ ] **Interface first** — define contracts in `application/interfaces/` before writing real implementations in `adapters/`
- [ ] **Anemic domain check** — entities have behaviour (methods), not just data fields
- [ ] **Value Objects** — used for primitives with business rules (e.g., `Money`, `Email`)
- [ ] **Composition Root** — concrete classes are instantiated in one place only (`main.ts` or DI module)
- [ ] **No cross-context contamination** — each Bounded Context owns its own models and DB transactions
- [ ] **No `any`** — use `error instanceof Error` guards in catch blocks, strict types everywhere
