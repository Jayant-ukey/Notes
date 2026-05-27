# 1. Event-Driven Architecture (EDA)

## What it is

**Event-Driven Architecture** is a design style where services communicate by producing and consuming **events** instead of direct API calls.

An **event** represents:

> “Something that has already happened in the system”

Example events:

* OrderCreated
* PaymentCompleted
* UserRegistered

---

## How it works

```text
Producer → Event → Broker → Consumers
```

Typical flow:

* Service publishes an event
* Message broker distributes it
* Multiple services react independently

Common tools:

* Apache Kafka
* RabbitMQ

---

## Example (E-commerce system)

1. Order Service → publishes `OrderCreated`
2. Payment Service → listens and processes payment
3. Inventory Service → reduces stock
4. Notification Service → sends email/SMS

All happen **independently and asynchronously**

---

## Key Characteristics

* Asynchronous communication
* Loose coupling
* Highly scalable
* Eventual consistency
* Reactive system

---

## Advantages

* Services are independent
* High scalability
* Better fault isolation
* Real-time processing possible

---

## Disadvantages

* Harder debugging
* Event ordering issues
* Event duplication handling required
* Eventual consistency (not strong consistency)

---

# 2. Domain-Driven Architecture (DDD)

## What it is

**Domain-Driven Design (DDD)** is a software design approach where the system is built around the **business domain and its logic**, not technical layers.

Focus:

> “Model the software based on real business rules and language.”

---

## Core idea

Instead of thinking:

* Controller → Service → DAO

We think:

* Business domains like Order, Payment, Inventory

---

## Key Building Blocks

### 1. Entity

Objects with identity

Example:

* Order
* User

---

### 2. Value Object

No identity, immutable

Example:

* Money
* Address

---

### 3. Aggregate

Group of related entities

Example:

* Order (aggregate root)

  * OrderItems
  * PaymentInfo

---

### 4. Repository

Data access abstraction

---

### 5. Domain Service

Business logic that doesn’t belong to a single entity

---

## Bounded Context (VERY IMPORTANT)

A **bounded context** defines boundaries of a domain model.

Example:

* Order Context → handles orders
* Payment Context → handles payments

Each context:

* Has its own model
* Has its own rules
* Communicates via APIs/events

---

## Example

E-commerce system:

* Order Domain
* Payment Domain
* Inventory Domain

Each is independent and aligned to business logic.

---

## Advantages

* Clear business modeling
* Easy to maintain complex systems
* Better collaboration with business teams
* Scalable architecture

---

## Disadvantages

* Complex to implement
* Requires strong domain understanding
* Overkill for simple systems

---

# 3. Key Difference (Important Interview Table)

| Feature    | Event-Driven Architecture  | Domain-Driven Design  |
| ---------- | -------------------------- | --------------------- |
| Focus      | Communication style        | Design approach       |
| Goal       | Asynchronous communication | Business modeling     |
| Key unit   | Event                      | Domain model          |
| Dependency | Message broker             | Business domain       |
| Coupling   | Loose                      | Context-based         |
| Example    | Kafka events               | Order/Payment domains |

---

# 4. How They Work Together (Very Important Answer)

Interviewers LOVE this.

You should say:

> “DDD defines *what the system is* (domain boundaries and business models), while Event-Driven Architecture defines *how services communicate*.”

---

## Combined example

* Order Domain (DDD bounded context)

  * publishes `OrderCreated` event
* Payment Domain

  * consumes event and processes payment
* Inventory Domain

  * updates stock

So:

* DDD → structure
* EDA → communication

---

# 5. Real Project Answer (Strong 5-year response)

> “In microservices architecture, we used Domain-Driven Design to define service boundaries like Order, Payment, and Inventory as separate bounded contexts. Each service owned its own data model and business rules.
>
> For inter-service communication, we used Event-Driven Architecture using Kafka. For example, when an order is created, Order Service publishes an OrderCreated event, which is consumed by Payment and Inventory services asynchronously. This helped us achieve loose coupling and scalability while ensuring eventual consistency.”

---

# 6. Common Interview Follow-ups

## Q1: Why use Event-Driven Architecture?

* Decoupling services
* Scalability
* Async processing
* Better resilience

---

## Q2: What is eventual consistency?

Data is not immediately consistent across services but becomes consistent over time.

---

## Q3: What is a bounded context?

A clear boundary where a specific domain model is valid and consistent.

---

## Q4: Can we use DDD without microservices?

Yes — DDD can be used in monoliths too.

---

# 7. Short 2-minute Interview Answer

> Event-Driven Architecture is a communication pattern where services interact through asynchronous events using message brokers like Kafka or RabbitMQ. It enables loose coupling and scalability but introduces eventual consistency.
>
> Domain-Driven Design is a software design approach where the system is modeled around business domains and rules using concepts like entities, value objects, aggregates, and bounded contexts.
>
> DDD defines the structure of the system, while event-driven architecture defines how those services communicate. In modern microservices systems, both are often used together to build scalable and maintainable architectures.
