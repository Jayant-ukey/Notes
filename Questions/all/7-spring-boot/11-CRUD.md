# Que - How do you perform CRUD operations (Create, Read, Update, Delete) in Spring Boot using JPA?

## ✅ Interview-ready answer

In Spring Boot using **Spring Data JPA**, CRUD operations are handled very efficiently using the `JpaRepository` interface. I don’t need to write SQL queries for basic operations because Spring Data JPA provides built-in implementations.

I typically implement CRUD in a layered architecture: **Controller → Service → Repository → Database**.

---

# 1. Create Operation (Insert)

To create a record, I use `save()` method.

```java id="c1"
public Employee createEmployee(Employee employee) {
    return employeeRepository.save(employee);
}
```

### Key point:

* If `id` is null → it performs **INSERT**
* If `id` exists → it may perform **UPDATE**

---

# 2. Read Operations (Fetch)

### (a) Get all records

```java id="c2"
public List<Employee> getAllEmployees() {
    return employeeRepository.findAll();
}
```

### (b) Get by ID

```java id="c3"
public Employee getEmployeeById(Long id) {
    return employeeRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("Employee not found"));
}
```

### Key point:

* `findAll()` → returns list
* `findById()` → returns `Optional`, so we must handle null safety properly

---

# 3. Update Operation

In JPA, update is also handled using `save()`.

```java id="c4"
public Employee updateEmployee(Long id, Employee updatedEmp) {
    Employee existing = employeeRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("Employee not found"));

    existing.setName(updatedEmp.getName());
    existing.setDepartment(updatedEmp.getDepartment());

    return employeeRepository.save(existing);
}
```

### Key point interviewers expect:

* JPA tracks entity state inside persistence context
* Update happens when entity is **managed and modified**

---

# 4. Delete Operation

```java id="c5"
public void deleteEmployee(Long id) {
    employeeRepository.deleteById(id);
}
```

### Alternative:

```java
employeeRepository.delete(employee);
```

---

# ⭐ What interviewers look for

* Understanding that `JpaRepository` provides CRUD out of the box
* Difference between:

  * `save()` for create and update
  * `findById()` returning `Optional`
* Entity lifecycle in JPA (managed vs detached)
* Proper service-layer abstraction (not calling repository directly from controller)

---

# 🚀 Production-level insights

I also mention these in real interviews:

* Always return DTOs instead of entities in APIs
* Use `@Transactional` for update/delete operations
* Handle exceptions properly using custom exceptions + `@ControllerAdvice`
* Avoid N+1 query issues when fetching related entities
* Use pagination (`Pageable`) for large datasets
* Soft delete (instead of hard delete) in enterprise systems

Example:

```java
public Page<Employee> getEmployees(Pageable pageable) {
    return employeeRepository.findAll(pageable);
}
```

---

# 🔁 Common follow-up questions

1. What is the difference between `save()` and `saveAndFlush()`?
2. How does JPA know whether to insert or update?
3. What is Entity state (Transient, Persistent, Detached)?
4. What happens internally when `deleteById()` is called?
5. How do you handle bulk updates in JPA?
6. What is the difference between `getById()` and `findById()`?

---

If you want, next I can:
👉 simulate a **real interviewer asking tricky follow-ups on CRUD (like persistence context, dirty checking, transaction behavior)** to test your depth.
