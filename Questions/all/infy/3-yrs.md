
# Java

### 1. What is Garbage Collection?

**Answer:**
Garbage Collection (GC) is a JVM mechanism that automatically removes objects that are no longer reachable or used by the application, helping reclaim heap memory and prevent memory leaks.

---

### 2. What is Dynamic Binding in Java?

**Answer:**
Dynamic Binding (Runtime Polymorphism) is the process where the method to be executed is determined at runtime rather than compile time.

**Example:**

```java
Animal animal = new Dog();
animal.bark();
```

If `bark()` is overridden in `Dog`, the `Dog` implementation is executed.

---

### 3. What are Strings in Java?

**Answer:**
A String is an immutable object used to represent text data.

```java
String str = "ABC";
str = "XYZ";
```

The original string `"ABC"` is not modified. A new string object `"XYZ"` is created.

---

### 4. Why are Strings Immutable?

**Answer:**

* Security
* Thread safety
* String pool optimization
* Better performance in caching and hashing

---

### 5. What should you use when performing many string concatenations?

**Answer:**
Use:

* `StringBuilder`
* `StringBuffer`

instead of String.

---

### 6. Difference Between StringBuilder and StringBuffer

| StringBuilder    | StringBuffer    |
| ---------------- | --------------- |
| Not synchronized | Synchronized    |
| Faster           | Slightly slower |
| Not thread-safe  | Thread-safe     |

---

### 7. What are the ways to create a Thread in Java?

**Answer:**

### Method 1: Extend Thread Class

```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Running");
    }
}
```

### Method 2: Implement Runnable Interface

```java
class MyTask implements Runnable {
    public void run() {
        System.out.println("Running");
    }
}
```

---

### 8. Which method must be overridden in Runnable?

**Answer:**

```java
public void run()
```

---

### 9. What are the Thread Life Cycle States?

**Answer:**

1. New
2. Runnable
3. Running
4. Waiting / Blocked / Timed Waiting
5. Terminated

Flow:

```
New
 ↓
Runnable
 ↓
Running
 ↓
Waiting/Blocked
 ↓
Runnable
 ↓
Terminated
```

---

# Collections

### 10. Why do we use HashMap?

**Answer:**
HashMap stores data as key-value pairs.

```java
Map<Integer,String> map = new HashMap<>();
```

Provides fast retrieval using keys.

---

### 11. What is Bucketing in HashMap?

**Answer:**
HashMap internally stores entries in buckets determined using the key's hash code.

```java
bucketIndex = hash(key) % capacity
```

---

### 12. Difference Between HashMap and ConcurrentHashMap

| HashMap                                   | ConcurrentHashMap          |
| ----------------------------------------- | -------------------------- |
| Not thread-safe                           | Thread-safe                |
| May throw ConcurrentModificationException | Supports concurrent access |
| Single-thread usage                       | Multi-thread usage         |

---

# Spring Boot

### 13. How do you connect Spring Boot to a database?

**Answer:**

### Step 1: Add dependency

```xml
spring-boot-starter-data-jpa
```

### Step 2: Configure properties

```properties
spring.datasource.url=
spring.datasource.username=
spring.datasource.password=
```

### Step 3: Create Entity

```java
@Entity
public class Employee {
}
```

### Step 4: Create Repository

```java
public interface EmployeeRepository
extends JpaRepository<Employee,Integer> {
}
```

---

### 14. Explain flow of a REST API that reads data from database

**Answer:**

```
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

Example:

```java
@GetMapping("/employees")
```

Repository methods:

```java
findAll()
findById()
```

---

### 15. Where should transactions be managed?

**Answer:**
Typically at the Service layer using:

```java
@Transactional
```

Business logic and transaction boundaries are generally maintained in services.

---

### 16. How do you monitor a Spring Boot application?

**Answer:**
Using Spring Boot Actuator.

Dependency:

```xml
spring-boot-starter-actuator
```

Endpoints:

```text
/actuator/health
/actuator/info
/actuator/metrics
```

---

### 17. Alternative to Actuator?

**Answer:**
Spring Boot Admin.

It provides a dashboard to monitor multiple Spring Boot applications.

---

### 18. Where do you deploy Spring Boot applications?

**Answer:**

Common options:

* Embedded Tomcat
* Liberty Server
* WebSphere
* Docker
* Kubernetes

---

# Microservices

### 19. Why Microservices over Monolithic Architecture?

**Answer:**

Advantages:

* Independent deployment
* Better scalability
* Easier maintenance
* Easier debugging
* Fault isolation
* Team independence

---

### 20. What components exist in your Microservices architecture?

**Answer:**

### API Gateway

Acts as entry point.

Responsibilities:

* Routing
* Authentication
* Filtering

---

### Service Discovery

Example:

Netflix Eureka

Used to discover service instances dynamically.

---

### Business Services

Examples:

* User Service
* Settings Service
* Order Service

---

### 21. What is an API Gateway?

**Answer:**
A gateway acts as the front door of the microservices ecosystem.

Flow:

```
Client
   ↓
API Gateway
   ↓
Microservices
```

---

### 22. What is Service Discovery?

**Answer:**
Services register themselves with a discovery server.

When one service wants to call another:

```
Service A
   ↓
Discovery Server
   ↓
Service B Address
```

---

# Database

### 23. Why do we use Indexes?

**Answer:**
Indexes improve query performance by reducing the amount of data scanned.

Example:

```sql
CREATE INDEX idx_name
ON employee(name);
```

---

### 24. What is Normalization?

**Answer:**
Normalization is the process of organizing database tables to reduce redundancy and improve data integrity.

Common normal forms:

* 1NF
* 2NF
* 3NF
* BCNF

---

### 25. What is a Primary Key?

**Answer:**
A column (or set of columns) that uniquely identifies each record in a table.

Example:

```sql
EmployeeId
```

Properties:

* Unique
* Not Null

---

### 26. What is a Foreign Key?

**Answer:**
A foreign key creates a relationship between two tables.

Example:

```sql
Orders.CustomerId
```

references

```sql
Customers.CustomerId
```

---

# Important Topics Repeated in This Interview

Focus heavily on:

### Java

* Garbage Collection
* String Immutability
* StringBuilder vs StringBuffer
* Threads
* Thread Lifecycle
* Dynamic Binding
* HashMap
* ConcurrentHashMap

### Spring Boot

* REST APIs
* Controller → Service → Repository flow
* JPA Repository
* Transactions
* Actuator
* Spring Boot Admin

### Microservices

* API Gateway
* Eureka Discovery Server
* Service Communication
* Benefits of Microservices

### Database

* Indexes
* Primary Key
* Foreign Key
* Normalization

These are exactly the areas that formed the majority of questions in this interview.
