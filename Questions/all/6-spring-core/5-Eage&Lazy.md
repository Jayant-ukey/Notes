# Que-  What is the difference between eager initialization and lazy initialization in Spring?

### 1. Direct Answer (What)

In Spring:

* **Eager initialization** means the bean is created **at application startup** when the Spring container is initialized.
* **Lazy initialization** means the bean is created **only when it is first requested/used**, not at startup.

👉 In short:

> Eager = created at startup
> Lazy = created on demand

---

### 2. Internal Understanding (How Spring handles it)

#### 🔹 Eager Initialization (Default behavior)

By default, Spring creates all **singleton beans eagerly** during application context startup.

Flow:

1. Spring scans beans
2. Instantiates all non-lazy singleton beans
3. Stores them in ApplicationContext

Example:

```java id="e1a1"
@Service
public class UserService {
    public UserService() {
        System.out.println("UserService created");
    }
}
```

👉 This will be created during application startup itself.

---

#### 🔹 Lazy Initialization

You can explicitly mark a bean as lazy:

```java id="e1a2"
@Service
@Lazy
public class UserService {
    public UserService() {
        System.out.println("UserService created");
    }
}
```

👉 This bean will NOT be created at startup.

It will only be created when:

```java id="e1a3"
applicationContext.getBean(UserService.class);
```

or when it is first injected and used.

---

### 3. Key Differences (Interview Table)

| Feature            | Eager Initialization | Lazy Initialization        |
| ------------------ | -------------------- | -------------------------- |
| Bean creation time | At startup           | On first use               |
| Default in Spring  | Yes                  | No                         |
| Startup time       | Slower               | Faster                     |
| Memory usage       | Higher upfront       | Optimized                  |
| Failure detection  | Early                | Delayed                    |
| Use case           | Core beans           | Rarely used or heavy beans |

---

### 4. Real-world Usage (Spring Boot Perspective)

#### 🔹 Eager initialization is used for:

* Controllers
* Services
* Repositories
* Core infrastructure beans

Because:

* They are always needed
* Early failure detection is important

---

#### 🔹 Lazy initialization is used for:

* Heavy objects (e.g., report generators, large caches)
* Rarely used services
* Optional integrations (e.g., third-party APIs)

Example:

```java id="e1a4"
@Service
@Lazy
public class PdfReportGenerator {
}
```

👉 If reports are rarely generated, we avoid startup cost.

---

### 5. Global Lazy Initialization (Advanced Spring Boot)

You can enable lazy initialization globally:

```properties id="e1a5"
spring.main.lazy-initialization=true
```

👉 This makes all beans lazy by default.

⚠️ Not commonly used in production because:

* It delays error detection
* First request latency increases

---

### 6. Best Practices / Production Considerations

* Prefer **eager initialization by default**
* Use lazy only for:

  * Heavy beans
  * Optional features
* Avoid global lazy initialization in production
* Be careful: lazy beans may fail at runtime instead of startup
* Combine with profiling (`@Profile`) for environment-specific beans

---

### 7. Key Points Interviewers Look For

* Understanding default behavior (eager singleton beans)
* Ability to explain startup vs runtime creation
* Awareness of trade-offs (performance vs early failure detection)
* Real-world use cases for lazy loading
* Knowledge of `@Lazy` and global lazy config
* Impact on memory and startup time

---

### 8. Common Follow-up Questions

1. What is the default bean scope and initialization type in Spring?
2. Does lazy initialization affect prototype beans?
3. What are the risks of using lazy initialization?
4. Can lazy beans cause runtime errors?
5. How does Spring proxy lazy beans?
6. What is the difference between lazy and prototype scope?
7. Can we use @Lazy at class and method level?

---

### One-Line Senior-Level Summary

> "Eager initialization creates Spring beans at application startup by default, ensuring early failure detection, while lazy initialization defers bean creation until it is first used, improving startup performance but shifting potential errors to runtime."
