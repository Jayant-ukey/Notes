# Que - How does caching work in Spring Boot?

## ✅ Interview-ready answer

In Spring Boot, **caching is a mechanism used to store frequently accessed data in memory (or external cache stores) so that repeated requests don’t hit the database every time.** This improves performance, reduces latency, and reduces load on the database.

Spring Boot provides a **declarative caching abstraction**, so we can enable caching with minimal configuration using annotations.

---

## 📌 How I explain it in an interview

Spring Boot caching works using a **cache abstraction layer**. We annotate methods with caching annotations, and Spring manages storing and retrieving data from the cache automatically using a **Cache Manager**.

---

# ⚙️ 1. Enable caching in Spring Boot

First, I enable caching at the application level:

```java id="c1"
@EnableCaching
@SpringBootApplication
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

---

# ⚙️ 2. Using `@Cacheable` (most important)

This annotation stores method results in cache.

```java id="c2"
@Cacheable(value = "employees", key = "#id")
public Employee getEmployeeById(Long id) {
    return repository.findById(id)
            .orElseThrow(() -> new RuntimeException("Not found"));
}
```

### 🔑 Behavior:

* First call → hits DB and stores result in cache
* Next calls → returns data from cache (no DB hit)

---

# ⚙️ 3. Updating cache (`@CachePut`)

Used when data changes and we want to update cache:

```java id="c3"
@CachePut(value = "employees", key = "#employee.id")
public Employee updateEmployee(Employee employee) {
    return repository.save(employee);
}
```

---

# ⚙️ 4. Removing cache (`@CacheEvict`)

Used when data is deleted or invalid:

```java id="c4"
@CacheEvict(value = "employees", key = "#id")
public void deleteEmployee(Long id) {
    repository.deleteById(id);
}
```

---

# 🧠 5. Cache Manager (internal working)

Spring uses a **CacheManager** to manage cache providers.

Default:

* Simple in-memory cache (`ConcurrentHashMap`)

Production cache providers:

* Redis (most common)
* EhCache
* Caffeine

---

# ⚡ 6. Example with Redis (production use case)

```properties id="c5"
spring.cache.type=redis
spring.redis.host=localhost
spring.redis.port=6379
```

---

# 🔁 7. Cache flow (important concept)

When a request comes:

1. Check cache
2. If data exists → return from cache
3. If not → call method → fetch from DB → store in cache → return response

---

# ⭐ Key points interviewers look for

* Spring caching is **annotation-driven abstraction**
* `@Cacheable` → read caching
* `@CachePut` → update cache
* `@CacheEvict` → remove cache
* Cache reduces DB load and improves performance
* Cache Manager decides underlying provider
* Works best for **read-heavy systems**

---

# 🚀 Production-level insights

* Use **Redis for distributed caching in microservices**
* Always define **TTL (Time To Live)** to avoid stale data
* Cache invalidation is the hardest problem (must be handled carefully)
* Avoid caching frequently changing data (like real-time balances)
* Use cache keys carefully to avoid collisions
* Monitor cache hit ratio in production

---

# ⚠️ Common pitfalls

* Stale data due to improper invalidation
* Over-caching small or frequently changing datasets
* Memory overflow in local caches
* Incorrect cache keys leading to wrong data

---

# 🔁 Common follow-up questions

1. What is the difference between `@Cacheable` and `@CachePut`?
2. What is cache eviction strategy?
3. How does Redis work as a distributed cache?
4. What is cache consistency problem?
5. What happens if cache goes down?
6. Difference between local cache and distributed cache?

---

If you want next, I can give you a **very common senior-level interview question:**
👉 *“How do you handle cache consistency in a microservices architecture?”*
