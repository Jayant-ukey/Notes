# Que - What is the purpose of JpaRepository?

## ✅ Interview-ready answer

In Spring Data JPA, **`JpaRepository` is an interface that provides built-in methods for performing database operations without writing boilerplate code.**

Its main purpose is to **abstract common CRUD operations and advanced JPA features**, so we can focus on business logic instead of SQL or EntityManager code.

---

## 📌 How I explain it in an interview

`JpaRepository` is part of Spring Data JPA and it extends `PagingAndSortingRepository` and `CrudRepository`, giving us a rich set of methods for working with the database.

So instead of writing implementations for insert, update, delete, or find operations, I simply extend `JpaRepository`.

---

## 🧱 Example

```java id="j1"
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
}
```

---

## ⚙️ What it provides out of the box

### 1. CRUD operations

* `save()` → insert/update
* `findById()`
* `findAll()`
* `deleteById()`
* `delete()`

---

### 2. Pagination and sorting

```java id="j2"
Page<Employee> findAll(Pageable pageable);
List<Employee> findAll(Sort sort);
```

---

### 3. Query derivation (very important feature)

We can define methods just by naming convention:

```java id="j3"
List<Employee> findByDepartment(String department);

List<Employee> findByNameAndDepartment(String name, String department);
```

Spring automatically generates SQL for these.

---

### 4. JPA-specific operations

* `flush()`
* `saveAndFlush()`
* `deleteInBatch()`
* `findAll(Specification<T>)` (with JPA Criteria API support)

---

## ⭐ Key points interviewers look for

* It reduces boilerplate DAO/repository code
* Built on top of `CrudRepository` + `PagingAndSortingRepository`
* Supports CRUD + pagination + sorting + custom queries
* Uses **dynamic proxy implementation at runtime**
* Integrates with Hibernate internally

---

## 🚀 Production-level insights

* Prefer `JpaRepository` over manual `EntityManager` for standard use cases
* Use method naming carefully; complex queries should use:

  * `@Query`
  * Criteria API
  * Specifications
* Use pagination for large datasets to avoid memory issues
* Avoid returning entities directly in APIs (use DTOs)
* Be mindful of lazy loading issues when using repository methods

Example with custom query:

```java id="j4"
@Query("SELECT e FROM Employee e WHERE e.salary > :salary")
List<Employee> findHighSalaryEmployees(@Param("salary") Double salary);
```

---

## 🔁 Common follow-up questions

1. Difference between `CrudRepository` and `JpaRepository`?
2. How does Spring implement `JpaRepository` internally?
3. What is query derivation in Spring Data JPA?
4. What is the role of proxy objects in repositories?
5. When would you avoid using `JpaRepository`?
6. Difference between `save()` and `saveAndFlush()`?

---

If you want next, I can challenge you with a **tricky interview follow-up:**
👉 *“If JpaRepository already provides save(), why do we still need @Transactional?”*
