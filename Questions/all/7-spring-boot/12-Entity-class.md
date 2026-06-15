# Que - What are Entity classes?

## ✅ Interview-ready answer

In Spring Boot with JPA, **Entity classes are Java classes that represent database tables**. Each instance of an entity corresponds to a row in the table, and each field maps to a column.

We use entity classes to define the **domain model**, and Hibernate (JPA provider) handles the mapping between Java objects and relational tables.

---

## 📌 Simple definition (how I say it in interview)

An **Entity class is a persistent Java object annotated with `@Entity` that is mapped to a database table and managed by JPA/Hibernate.**

---

## 🧱 Example

```java id="e1"
@Entity
@Table(name = "employees")
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    private String department;

    // getters and setters
}
```

---

## 🔑 Key annotations used

### 1. `@Entity`

* Marks the class as a JPA entity
* Mandatory for persistence

### 2. `@Table`

* Specifies table name (optional if class name matches table name)

### 3. `@Id`

* Defines primary key

### 4. `@GeneratedValue`

* Defines auto-generation strategy for primary key

---

## ⚙️ How it works internally

When the application runs:

* Hibernate scans all `@Entity` classes
* Creates mapping metadata
* Uses them to generate SQL queries dynamically
* Manages object state through **Persistence Context**

---

## ⭐ Key points interviewers look for

* Entity = Java class mapped to DB table
* Must have a primary key (`@Id`)
* Managed by JPA/Hibernate
* Part of ORM (Object Relational Mapping)
* Works with repository layer (`JpaRepository`)

---

## 🚀 Production-level insights

* Entities should be **simple POJOs (no business logic inside)**
* Avoid exposing entities directly in APIs → use DTOs
* Use proper relationships:

  * `@OneToOne`
  * `@OneToMany`
  * `@ManyToOne`
  * `@ManyToMany`
* Be careful with **fetch type (LAZY vs EAGER)** to avoid performance issues
* Avoid large object graphs in entities (can cause N+1 and memory issues)

Example of relationship:

```java id="e2"
@OneToMany(mappedBy = "employee", fetch = FetchType.LAZY)
private List<Project> projects;
```

---

## 🔁 Common follow-up questions

1. What is the difference between Entity and DTO?
2. What is the lifecycle of an entity?
3. What is persistence context in Hibernate?
4. What happens when an entity is updated?
5. Difference between `@Entity` and `@Table`?
6. What are different fetching strategies?

---

If you want, next I can go deeper and ask:
👉 **“What happens internally when an Entity is saved in JPA?”** (very common 5-year-level follow-up)
