# Que - WHich annotations are used in your projects?

### 1. Direct Answer (What)

In my Spring Boot projects, I typically use a **combination of core Spring, Spring Boot, and enterprise-level annotations** based on layered architecture (Controller–Service–Repository) and cross-cutting concerns like transactions, validation, and configuration.

---

### 2. Project-Level Annotations I commonly use (with purpose)

#### 🔹 1. Application Bootstrapping

```java id="p1a1"
@SpringBootApplication
public class OrderServiceApplication {
}
```

👉 Entry point of the application (component scan + auto configuration)

---

#### 🔹 2. Layered Architecture

**Controller layer:**

```java id="p1a2"
@RestController
@RequestMapping("/api/orders")
```

👉 Used for REST APIs and request mapping

---

**Service layer:**

```java id="p1a3"
@Service
```

👉 Business logic layer

---

**Repository layer:**

```java id="p1a4"
@Repository
```

👉 Database access layer (Spring Data JPA)

---

#### 🔹 3. Dependency Injection

```java id="p1a5"
@Autowired
```

👉 Injecting dependencies (though I prefer constructor injection)

---

Preferred modern approach:

```java id="p1a6"
public OrderService(PaymentService paymentService) {
    this.paymentService = paymentService;
}
```

---

#### 🔹 4. Configuration & Bean Management

```java id="p1a7"
@Configuration
@Bean
```

👉 For defining custom beans or third-party integrations

---

#### 🔹 5. External Configuration

```java id="p1a8"
@ConfigurationProperties
@Value
```

👉 Injecting configuration from application.properties or YAML

---

#### 🔹 6. Transaction Management

```java id="p1a9"
@Transactional
```

👉 Ensures ACID behavior in service methods

---

#### 🔹 7. Validation (Very commonly used in APIs)

```java id="p1b1"
@Valid
@NotNull
@Size
@Email
```

👉 Request validation in REST APIs

---

Example:

```java id="p1b2"
public class UserRequest {

    @NotNull
    private String name;

    @Email
    private String email;
}
```

---

#### 🔹 8. JPA / Persistence layer

```java id="p1b3"
@Entity
@Table
@Id
@GeneratedValue
```

👉 Mapping Java objects to database tables

---

#### 🔹 9. Lombok (to reduce boilerplate)

```java id="p1b4"
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
@Builder
```

👉 Reduces getters/setters and constructor code

---

#### 🔹 10. Exception Handling

```java id="p1b5"
@ControllerAdvice
@ExceptionHandler
@ResponseStatus
```

👉 Centralized exception handling

---

#### 🔹 11. Logging / AOP (in some projects)

```java id="p1b6"
@Aspect
@Before
@After
```

👉 Used for logging, auditing, and cross-cutting concerns

---

### 3. Real-world usage pattern (important interview insight)

In a typical Spring Boot microservice:

```text id="p1b7"
Controller → @RestController
Service → @Service + @Transactional
Repository → @Repository
Entity → @Entity
Config → @Configuration / @ConfigurationProperties
Validation → @Valid + constraints
Exception → @ControllerAdvice
```

---

### 4. Best Practices I follow

✔ Use constructor injection instead of `@Autowired` field injection
✔ Prefer `@ConfigurationProperties` over `@Value`
✔ Keep controllers thin and services heavy
✔ Use `@Transactional` only in service layer
✔ Centralized exception handling using `@ControllerAdvice`
✔ Avoid mixing business logic in controllers
✔ Use Lombok carefully (avoid overuse in complex domains)

---

### 5. Key Points Interviewers Look For

* Awareness of **layered architecture annotations**
* Understanding of **core Spring vs Spring Boot annotations**
* Real-world usage, not just theory
* Proper use of:

  * DI
  * Transaction
  * Validation
  * JPA
* Clean architecture practices
* Preference for constructor injection

---

### 6. Common Follow-up Questions

1. Why do we use @Service instead of @Component?
2. Difference between @Controller and @RestController?
3. Why prefer constructor injection over @Autowired?
4. What is @Transactional and how does it work internally?
5. Difference between @Value and @ConfigurationProperties?
6. What is @ControllerAdvice used for?
7. How does Spring handle validation using @Valid?

---

### One-Line Senior-Level Summary

> "In my Spring Boot projects, I use a combination of annotations across layers like @RestController, @Service, @Repository, @Entity, @Configuration, @Transactional, and validation annotations to implement a clean layered architecture with proper dependency injection, transaction management, and configuration handling."
