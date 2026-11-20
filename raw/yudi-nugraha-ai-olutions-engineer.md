---
title: "Yudi Nugraha - AI Solutions Engineer"
source: "https://yudinugraha.com/en/blog/modular-monolith-nodejs?utm_source=chatgpt.com"
author:
published: 2026-05-03
created: 2026-11-20
description: "Helping businesses solve complex challenges through technology, automation, and AI."
tags:
  - "clippings"
---
In the [previous post](https://yudinugraha.com/blog/folder-structure-evolution-practical-nodejs), we built the Orders feature using five architectures — from Simple MVC to Microservices. Each one was tested and compared side by side.

But there's one architecture we haven't covered: **Modular Monolith**.

It sits right between Feature-Based and Microservices on the architectural spectrum. A single deployment unit like any monolith, but with module boundaries that are enforced explicitly — not just by convention.

## The Problem It Solves

In Feature-Based Structure (NestJS), this is perfectly valid:

```
// modules/orders/orders.service.ts
import { NotificationsService } from '../notifications/notifications.service';
```

A direct import into another module's internal class. Nothing prevents it. No one notices until the coupling becomes painful — usually during a refactoring or when trying to split modules into separate services.

Modular Monolith enforces one simple rule: **other modules may only access what you explicitly expose**.

\---

## The Scenario: Orders Feature

Same as the previous series:

- `POST /orders` — create a new order
- `GET /orders/:id` — fetch order details
- Validation: product stock must be available
- Notification: send email after successful order

\---

## Folder Structure

```
/src
  /modules
    /orders
      /public
        index.ts              ← the only entry point to this module
        orders.module.ts
        create-order.dto.ts
        order-created.event.ts
        notification.port.ts  ← interface orders needs from outside
      /internal
        orders.controller.ts
        orders.service.ts
        orders.repository.ts
        order.entity.ts
        orders.service.spec.ts
    /notifications
      /public
        index.ts
        notifications.module.ts
        notification-adapter.ts
      /internal
        notifications.service.ts
        notifications.service.spec.ts
    /products
      /public
        index.ts
        products.module.ts
        product.port.ts
      /internal
        products.service.ts
  /shared
    /events
      in-process-event-bus.ts
    /kernel
      base.entity.ts
```

**The rules:**

- Other modules may only import from `modules/*/public/index.ts`
- Nothing may import directly from `modules/*/internal/`
- Enforced by `eslint-plugin-boundaries` — not just team convention

\---

## Public API per Module

The `index.ts` in the `/public` folder is the module's official contract — what's exported here is what other modules are allowed to use.

```
// modules/orders/public/index.ts
export { OrdersModule } from './orders.module';
export { CreateOrderDto } from './create-order.dto';
export { OrderCreatedEvent } from './order-created.event';
export { INotificationPort } from './notification.port';

// OrdersService, OrderRepository, Order entity are NOT exported
// Anyone trying to import them directly will be blocked by ESLint
```

Other modules consume it like this:

```
// CORRECT — only from the public API
import { CreateOrderDto, OrderCreatedEvent } from '@modules/orders/public';

// WRONG — directly into internal (ESLint error)
import { OrdersService } from '@modules/orders/internal/orders.service';
```

\---

## Orders Module Implementation

The `orders` module does not import `NotificationsService` directly. It only knows about `INotificationPort` — an interface defined in the `orders` module's own public API.

```
// modules/orders/public/notification.port.ts
export interface INotificationPort {
  sendOrderConfirmation(orderId: string): Promise<void>;
}
```
```
// modules/orders/internal/orders.service.ts
import { Injectable, Inject } from '@nestjs/common';
import { INotificationPort } from '../public/notification.port';
import { OrdersRepository } from './orders.repository';

@Injectable()
export class OrdersService {
  constructor(
    private readonly repo: OrdersRepository,
    @Inject('NOTIFICATION_PORT')
    private readonly notifier: INotificationPort,
  ) {}

  async createOrder(dto: CreateOrderDto) {
    const product = await this.repo.findProductById(dto.productId);
    if (!product || product.stock < dto.quantity) {
      throw new Error('Insufficient stock');
    }

    const order = await this.repo.save({
      productId: dto.productId,
      quantity: dto.quantity,
    });

    await this.notifier.sendOrderConfirmation(order.id);
    return order;
  }
}
```
```
// modules/orders/public/orders.module.ts
import { Module } from '@nestjs/common';
import { OrdersService } from '../internal/orders.service';
import { OrdersRepository } from '../internal/orders.repository';
import { OrdersController } from '../internal/orders.controller';

@Module({
  controllers: [OrdersController],
  providers: [OrdersService, OrdersRepository],
  exports: [OrdersService],
})
export class OrdersModule {}
```

\---

## Inter-Module Communication

Two patterns are available. Both are valid depending on your needs.

### Pattern 1 — Interface Injection

The `orders` module defines the port it needs. The `notifications` module provides the implementation, wired together in `AppModule`.

```
// modules/notifications/public/notification-adapter.ts
import { Injectable } from '@nestjs/common';
import { INotificationPort } from '@modules/orders/public';
import { NotificationsService } from '../internal/notifications.service';

@Injectable()
export class NotificationAdapter implements INotificationPort {
  constructor(private readonly service: NotificationsService) {}

  async sendOrderConfirmation(orderId: string): Promise<void> {
    await this.service.send({ type: 'order_confirmed', orderId });
  }
}
```
```
// app.module.ts — composition root
@Module({
  imports: [OrdersModule, NotificationsModule],
  providers: [
    {
      provide: 'NOTIFICATION_PORT',
      useClass: NotificationAdapter,
    },
  ],
})
export class AppModule {}
```

### Pattern 2 — In-Process Event Bus

The `orders` module publishes an event. The `notifications` module subscribes. Neither knows about the other.

```
// modules/orders/internal/orders.service.ts
async createOrder(dto: CreateOrderDto) {
  const order = await this.repo.save(dto);
  await this.eventBus.publish(new OrderCreatedEvent(order.id, dto.customerEmail));
  return order;
}

// modules/notifications/internal/notifications.service.ts
@OnEvent('OrderCreatedEvent')
async handleOrderCreated(event: OrderCreatedEvent) {
  await this.sendEmail(event.customerEmail, event.orderId);
}
```

**When to choose each:**

- **Interface injection** — when you need synchronous feedback (return values) from another module
- **Event bus** — when the communication can be async and you want maximum decoupling

\---

## Boundary Enforcement with ESLint

Without tooling, the `/public` and `/internal` convention is only moral. One developer in a hurry can ignore it, and no one will know until the damage compounds.

Setup `eslint-plugin-boundaries`:

```
npm install --save-dev eslint-plugin-boundaries
```
```
// .eslintrc.js
module.exports = {
  plugins: ['boundaries'],
  settings: {
    'boundaries/elements': [
      {
        type: 'module-public',
        pattern: 'src/modules/*/public/index.ts',
      },
      {
        type: 'module-internal',
        pattern: 'src/modules/*/internal/**',
      },
      {
        type: 'shared',
        pattern: 'src/shared/**',
      },
    ],
  },
  rules: {
    'boundaries/element-types': [
      'error',
      {
        default: 'disallow',
        rules: [
          {
            from: ['module-public', 'module-internal'],
            allow: ['module-public', 'shared'],
          },
        ],
      },
    ],
  },
};
```

Now if anyone writes:

```
import { OrdersService } from '@modules/orders/internal/orders.service';
```

ESLint immediately throws an error — in the editor and in the CI pipeline.

\---

## Database: Table Prefix per Module

One database, but each module uses a distinct table prefix to prevent direct cross-module queries.

```
// modules/orders/internal/order.entity.ts
@Entity('orders__orders') // prefix: orders__
export class OrderEntity {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column()
  productId: string;

  @Column()
  quantity: number;
}

// modules/notifications/internal/notification-log.entity.ts
@Entity('notifications__logs') // prefix: notifications__
export class NotificationLogEntity {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column()
  orderId: string;
}
```

The `orders` module must not query `notifications__*` tables directly. If it needs data from `notifications`, it must go through the module's public API.

\---

## Testing in Modular Monolith

### Unit test within the module

Identical to Feature-Based — using `Test.createTestingModule()` in NestJS.

```
// modules/orders/internal/orders.service.spec.ts
describe('OrdersService', () => {
  let service: OrdersService;
  let mockRepo: jest.Mocked<OrdersRepository>;
  let mockNotifier: jest.Mocked<INotificationPort>;

  beforeEach(async () => {
    const module = await Test.createTestingModule({
      providers: [
        OrdersService,
        {
          provide: OrdersRepository,
          useValue: { findProductById: jest.fn(), save: jest.fn() },
        },
        {
          provide: 'NOTIFICATION_PORT',
          useValue: { sendOrderConfirmation: jest.fn() },
        },
      ],
    }).compile();

    service = module.get(OrdersService);
    mockRepo = module.get(OrdersRepository);
    mockNotifier = module.get('NOTIFICATION_PORT');
  });

  it('throws when stock is insufficient', async () => {
    mockRepo.findProductById.mockResolvedValue({ stock: 0 });

    await expect(
      service.createOrder({ productId: 'p1', quantity: 2 })
    ).rejects.toThrow('Insufficient stock');
  });

  it('sends notification after successful order', async () => {
    mockRepo.findProductById.mockResolvedValue({ stock: 10 });
    mockRepo.save.mockResolvedValue({ id: 'order-1' });

    await service.createOrder({ productId: 'p1', quantity: 2 });

    expect(mockNotifier.sendOrderConfirmation).toHaveBeenCalledWith('order-1');
  });
});
```

### Module contract test — test only through the public API

```
// modules/orders/orders.module.spec.ts
describe('OrdersModule — public API', () => {
  it('creates order via public interface only', async () => {
    const module = await Test.createTestingModule({
      imports: [OrdersModule],
    })
      .overrideProvider('NOTIFICATION_PORT')
      .useValue({ sendOrderConfirmation: jest.fn() })
      .compile();

    const service = module.get(OrdersService);
    const result = await service.createOrder({ productId: 'p1', quantity: 2 });

    expect(result.id).toBeDefined();
  });
});
```

This test verifies the module's public API works end-to-end without touching internal implementation.

### Architecture test — ESLint in CI

```
# package.json
"test:arch": "eslint src --rule '{\"boundaries/element-types\": \"error\"}' --ext .ts"
```
```
# CI pipeline
npm run test:arch
```

If any illegal cross-module import exists, the pipeline fails with a clear message before the code reaches the main branch.

\---

## Comparison

| Aspect | Feature-Based | Modular Monolith | Microservices |
| --- | --- | --- | --- |
| Module boundary | Folder convention | ESLint-enforced public API | Network boundary |
| Deployment | 1 unit | 1 unit | N units |
| Operational complexity | Low | Low | High |
| Contract testing | Not needed | Not needed | Required (Pact.js) |
| Migration to microservices | Hard | Easy | N/A |
| Database | Shared | Shared, prefix per module | Separate |

\---

## When to Use Modular Monolith

Modular Monolith is the right choice when:

- The team has outgrown Feature-Based and cross-module coupling is becoming painful
- The system may be split into microservices in the future — clean boundaries now make that easier
- You have 10–30 developers who still want a single deployment
- The system has high data consistency requirements incompatible with distributed transactions

\---

## Conclusion

Modular Monolith isn't just a tidier version of Feature-Based. The fundamental difference lies in boundary enforcement: convention vs. tooling.

With `eslint-plugin-boundaries`, every violation is caught automatically — in the editor before committing, and in CI before merging. No one can accidentally access another module's internals.

That makes Modular Monolith a significantly stronger foundation for growth — both in team size and system complexity.

\---

## Closing Thoughts

The right question isn't:

\> "When should we move to microservices?"

It's:

\> "Are our domain boundaries clear enough to be separated?"

Modular Monolith helps answer that question — before you have to pay the operational cost of a distributed system.