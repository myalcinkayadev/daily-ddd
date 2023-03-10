# daily-ddd

### Concepts:
1. Bounded Context: This is used to define a specific area or "context" in which a particular domain model applies.
2. Domain Model: The set of objects and their relationships that represent the problem or business domain that the application is being built to solve.
3. Entities: These are objects that have a unique identity and are used to represent things that exist in the real world, such as customers or orders.
4. Value Objects: These are objects that don't have a unique identity and are used to represent simple concepts such as money or a date.
5. Services: These are objects that perform a specific action or calculation, such as calculating shipping costs or generating a report.
6. Modules: These are used to organize the code and provide a clear separation of concerns.
7. Domain Events: These are events that occur within the domain, such as a customer placing an order or a product running out of stock.
8. Aggregates: These are groups of related objects that are treated as a single unit in the domain model.
9. Factories: These are used to create new objects in the domain model, such as creating new customer or order objects.
10. Repositories: These provide a way to store and retrieve objects from a persistent storage.
11. Domain Services: These are services that perform operations that don't fit naturally into any entity or value object.
12. Application Services: These are services that coordinate the use of domain services and other components to implement a use case.
13. Specification: These are used to define a set of criteria that an object must meet.
14. Domain Policy: These are used to encapsulate the business rules and constraints of the domain.
15. Domain-Specific Language (DSL): This is a language that is specific to the domain and is used to express the domain concepts in a natural and intuitive way.
16. Ubiquitous Language: This is the language that is used by the domain experts and developers to express the concepts of the domain.
17. Context Map: This is a visual representation of the relationship between different bounded contexts in the system.
18. Strategic Design: This is the process of defining the overall architecture of the system to align with the business goals.
19. Tactical Design: This is the process of implementing the details of the domain model and its components.
20. Anti-corruption layer: This is a layer that separates the domain model from external systems and ensures that any changes in the external systems do not affect the integrity of the domain model.

Domain events:
```typescript
class OrderCreatedEvent {
    constructor(public readonly orderId: string) {}
}

class OrderShippedEvent {
    constructor(public readonly orderId: string, public readonly trackingNumber: string) {}
}
```

Aggregates:
```typescript
class Order {
    private readonly events: Array<OrderCreatedEvent | OrderShippedEvent> = [];

    constructor(private readonly orderId: string) {
        this.events.push(new OrderCreatedEvent(orderId));
    }

    ship(trackingNumber: string) {
        this.events.push(new OrderShippedEvent(this.orderId, trackingNumber));
    }

    getEvents() {
        return this.events;
    }
}

const order = new Order("123");
order.ship("456");
console.log(order.getEvents());
```

Repositories:
```typescript
interface OrderRepository {
    findById(id: number): Order | undefined;
    save(order: Order): void;
}
```

Domain services:
```typescript
class OrderService {
    constructor(private repository: OrderRepository) {}

    approveOrder(order: Order): void {
        // Order approval logic goes here
        this.repository.save(order);
    }
}
```

[Specification:](https://github.com/myalcinkayadev/daily-snippets-ts#specification-pattern)
```typescript
interface Specification<T> {
    isSatisfiedBy(candidate: T): boolean;
}

class CustomerHasOrderedSpecification implements Specification<Customer> {
    isSatisfiedBy(customer: Customer): boolean {
        // Specification implementation goes here
    }
}
```

Domain policy:
```typescript
class OrderPolicy {
    canApprove(order: Order, user: User): boolean {
        // Policy implementation goes here
    }
}
```

Anti-corruption layer:
```typescript
class ExternalOrderService {
    constructor(private externalApi: ExternalApi) {}

    getOrder(id: number): Order {
        // Retrieve order from external API
        const externalOrder = this.externalApi.getOrder(id);
        // Convert external order to internal order
        return new Order(
            externalOrder.id,
            externalOrder.customerId,
            externalOrder.items.map(i => new OrderItem(i.productId, i.quantity))
        );
    }
}
```
