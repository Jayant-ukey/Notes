“Entity mapping” in Java/Spring interviews usually refers to **JPA/Hibernate ORM mapping between Java classes and database tables**.

For a **5-year experienced candidate**, you should explain:

* What entity mapping is
* Types of relationships
* Annotations
* Fetching strategies
* Cascade behavior
* Real Spring Boot usage

---

# What is Entity Mapping?

Entity mapping is the process of mapping a Java object (Entity) to a database table using ORM frameworks like Hibernate or Spring Data JPA.

It allows us to work with Java objects instead of writing SQL queries.

---

# Basic Example

```java id="q1m8kz"
@Entity
@Table(name = "employee")
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
}
```

### Mapping meaning:

* Class → Table
* Object → Row
* Field → Column

---

# Types of Entity Relationships

This is the most important part in interviews.

---

# 1. One-to-One Mapping

### Example: User ↔ Profile

```java id="x7p2kd"
@Entity
class User {

    @OneToOne
    @JoinColumn(name = "profile_id")
    private Profile profile;
}
```

```java id="m4t9qz"
@Entity
class Profile {

    @Id
    private Long id;
}
```

---

### Key point:

* One user has one profile
* One profile belongs to one user

---

# 2. One-to-Many Mapping

### Example: Department → Employees

```java id="k8q0wp"
@Entity
class Department {

    @OneToMany(mappedBy = "department")
    private List<Employee> employees;
}
```

```java id="v6r3ld"
@Entity
class Employee {

    @ManyToOne
    @JoinColumn(name = "dept_id")
    private Department department;
}
```

---

### Key point:

* One department → many employees

---

# 3. Many-to-One Mapping

This is inverse of One-to-Many.

Used on child side (Employee side above).

---

# 4. Many-to-Many Mapping

### Example: Student ↔ Course

```java id="n9x2qp"
@Entity
class Student {

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private List<Course> courses;
}
```

```java id="c3m7zx"
@Entity
class Course {

    @ManyToMany(mappedBy = "courses")
    private List<Student> students;
}
```

---

# Fetch Types

Important interview topic.

## 1. EAGER Fetching

* Loads related data immediately

```java id="e1q7mv"
@OneToMany(fetch = FetchType.EAGER)
```

Problem:

* Can cause performance issues (loads unnecessary data)

---

## 2. LAZY Fetching (Default)

* Loads data only when needed

```java id="l7p0kc"
@OneToMany(fetch = FetchType.LAZY)
```

Better for performance.

---

# Cascade Types

Cascade defines how operations propagate.

```java id="c9m1zt"
@OneToMany(cascade = CascadeType.ALL)
```

## Types:

* ALL
* PERSIST
* MERGE
* REMOVE

---

### Example:

If you save Department → Employees also saved automatically.

---

# Bidirectional vs Unidirectional Mapping

## Unidirectional:

* One side knows about the relationship

## Bidirectional:

* Both sides know each other

Example:

* Department ↔ Employee

---

# Common Problems in Entity Mapping

## 1. N+1 Query Problem

Occurs when:

* One query loads parent
* Then multiple queries load children

Solution:

* Fetch join
* Entity graph
* Batch size

---

## 2. Infinite Recursion (JSON Serialization)

Example:

* Employee → Department → Employee → loop

Solution:

* @JsonManagedReference
* @JsonBackReference
* or DTO usage

---

# Real Spring Boot Usage

In real projects:

* Entities represent DB tables
* Repositories handle CRUD
* Services handle business logic

Example flow:

```text id="z3m8qk"
Controller → Service → Repository → Entity → Database
```

---

# Real Project Answer (Important)

You can say:

> “In our Spring Boot microservices, we used JPA entity mapping extensively with Hibernate. We defined relationships like One-to-Many between Department and Employee and Many-to-Many between User and Roles. We used LAZY loading by default and handled performance issues like N+1 problem using fetch joins and DTO projections. Cascade operations were carefully controlled to avoid unintended deletes or updates.”

---

# Common Interview Questions

## What is @Entity?

Marks class as database table mapping.

---

## Difference between mappedBy and JoinColumn?

| mappedBy       | JoinColumn  |
| -------------- | ----------- |
| Inverse side   | Owning side |
| No FK creation | FK defined  |

---

## Why LAZY loading preferred?

Improves performance by loading data only when needed.

---

## What causes N+1 problem?

When Hibernate executes:

* 1 query for parent
* N queries for children

---

# Short Crisp Interview Answer

> Entity mapping in JPA is the process of mapping Java classes to database tables using ORM frameworks like Hibernate. It defines relationships such as One-to-One, One-to-Many, Many-to-One, and Many-to-Many using annotations like @OneToMany and @ManyToOne.
>
> We also manage fetch strategies (LAZY/EAGER), cascade operations, and handle performance issues like N+1 problem. In Spring Boot, entity mapping helps in eliminating manual SQL and enables object-relational interaction through repositories.
