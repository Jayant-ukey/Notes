# Que - Do you know the design patterns in Java? OR How comfortable are you with design patterns?

### 1. Direct Answer (What)

Yes, I’m comfortable with **design patterns in Java**. I’ve used them extensively in Spring Boot and enterprise applications to write **scalable, maintainable, and loosely coupled code**.

Design patterns are **proven, reusable solutions** to common software design problems. They are generally grouped into:

* **Creational patterns** (object creation)
* **Structural patterns** (class/object composition)
* **Behavioral patterns** (object interaction)

---

### 2. Internal Understanding (How)

In real systems, design patterns help solve problems like:

* Tight coupling between classes
* Complex object creation logic
* Code duplication
* Hard-to-maintain business logic flows

In Spring Boot, many patterns are already used implicitly by the framework:

* Dependency Injection → Inversion of Control (IoC)
* Bean creation → Factory Pattern
* Proxies for AOP → Proxy Pattern

---

### 3. Common Patterns I’ve Used (Real-world Experience)

#### 🔹 1. Singleton Pattern

Used when only one instance is required.

Example:

* Logger
* Configuration classes

Spring example:

* By default, Spring beans are Singleton scoped.

```java id="s1a2bc"
@Component
public class AppConfig {
}
```

---

#### 🔹 2. Factory Pattern

Used to create objects without exposing instantiation logic.

Example in Spring:

* `BeanFactory`, `ApplicationContext`

Custom example:

* Payment gateway selection (UPI, Card, NetBanking)

---

#### 🔹 3. Strategy Pattern

Used to switch algorithms at runtime.

Real-world example:

* Payment method selection
* Sorting strategies
* Discount calculation

```java id="p4k8xy"
interface PaymentStrategy {
    void pay(double amount);
}
```

Spring Boot usage:

* Different service implementations injected based on condition (`@Qualifier` or `@Profile`)

---

#### 🔹 4. Observer Pattern

Used for event-driven systems.

Example:

* Spring Application Events
* Kafka event consumers

```java id="o9z3mn"
@EventListener
public void handleOrderEvent(OrderEvent event) {
}
```

---

#### 🔹 5. Proxy Pattern

Used for:

* AOP (logging, security, transactions)

Example:

* `@Transactional` in Spring uses proxies internally

---

#### 🔹 6. Builder Pattern

Used for complex object creation.

Example:

* `StringBuilder`
* Lombok `@Builder`

```java id="b6x9rt"
User user = User.builder()
    .name("John")
    .age(30)
    .build();
```

---

### 4. Best Practices / Production Considerations

* Don’t force patterns everywhere → use only when needed
* Prefer **simplicity over over-engineering**
* In Spring Boot, leverage framework patterns instead of reinventing
* Combine patterns (e.g., Strategy + Factory) for flexible systems
* Ensure patterns improve **readability and extensibility**

---

### 5. Key Points Interviewers Look For

* Clear classification: Creational, Structural, Behavioral
* Real-world usage, not just definitions
* Awareness that Spring internally uses patterns
* Ability to explain **why and when to use patterns**
* Examples like Strategy, Factory, Singleton, Proxy
* Understanding trade-offs (over-engineering risk)

---

### 6. Common Follow-up Questions

1. What is the difference between Factory and Abstract Factory?
2. How does Spring use Proxy Pattern internally?
3. What is the difference between Strategy and State pattern?
4. Can Singleton break in distributed systems?
5. What design patterns are used in Spring Boot internally?
6. When should you avoid design patterns?
7. How does Dependency Injection relate to design patterns?
8. Can you combine multiple design patterns in a real system?

---

### One-Line Senior-Level Summary

> "Yes, I’m comfortable with design patterns and have used them in real Spring Boot applications—especially Strategy, Factory, Singleton, and Proxy patterns—to build loosely coupled, maintainable, and scalable systems, while also leveraging the fact that Spring internally implements many of these patterns."
