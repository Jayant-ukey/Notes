# Que - What is the purpose of CrudRepository?

## ✅ Interview-ready answer

`CrudRepository` in Spring Data JPA is the **most basic repository interface that provides standard CRUD (Create, Read, Update, Delete) operations for an entity** without requiring any implementation.

It is part of Spring Data’s repository abstraction and is used to reduce boilerplate DAO code.

---

## 📌 How I explain it in an interview

`CrudRepository` is an interface that provides generic methods to perform basic database operations on an entity. We simply extend it and Spring automatically provides the implementation at runtime using proxies.

---

## 🧱 Example

```java id="c1"
public interface EmployeeRepository extends CrudRepository<Employee, Long> {
}
```

---

## ⚙️ What it provides

### 1. Create / Update

```java id="c2"
<S extends T> S save(S entity);
```

* Inserts if entity is new
* Updates if entity already exists (based on primary key)

---

### 2. Read operations

```java id="c3"
Optional<Employee> findById(Long id);

Iterable<Employee> findAll();
```

---

### 3. Delete operations

```java id="c4"
void deleteById(Long id);

void delete(Employee entity);

void deleteAll();
```

---

### 4. Existence check

```java id="c5"
boolean existsById(Long id);
```

---

## 🔑 Key points interviewers look for

* It is the **base repository interface in Spring Data**
* Provides **basic CRUD operations**
* Uses **generics** (`<T, ID>`) for type safety
* No implementation required (Spring generates proxy at runtime)
* `JpaRepository` extends `CrudRepository`

---

## 📊 CrudRepository vs JpaRepository (important comparison)

| Feature              | CrudRepository | JpaRepository |
| -------------------- | -------------- | ------------- |
| CRUD operations      | Yes            | Yes           |
| Pagination           | ❌ No           | ✅ Yes         |
| Sorting              | ❌ No           | ✅ Yes         |
| JPA-specific methods | ❌ Limited      | ✅ Extensive   |
| Batch operations     | ❌ No           | ✅ Yes         |

👉 In real projects, we almost always prefer `JpaRepository` over `CrudRepository`.

---

## 🚀 Production-level insights

* `CrudRepository` is useful for **simple microservices or minimal persistence layers**
* In enterprise systems, `JpaRepository` is preferred due to pagination + advanced JPA support
* Avoid overusing repository methods directly in controllers → always use service layer
* Be cautious with `findAll()` on large tables (can cause memory issues)
* Always handle `Optional` properly from `findById()`

---

## 🔁 Common follow-up questions

1. Difference between `CrudRepository` and `JpaRepository`?
2. What is the difference between `save()` and `saveAll()`?
3. Why does Spring use interfaces instead of concrete classes?
4. What happens internally when `findById()` is called?
5. What is the role of proxy objects in repositories?
6. When would you use `CrudRepository` instead of `JpaRepository`?

---

If you want, next I can ask you a **real interview trap question:**
👉 *“Why do we need CrudRepository or JpaRepository at all when we already have EntityManager?”*
