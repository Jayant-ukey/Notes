## JDBC Connectivity —

### What is JDBC?

**JDBC (Java Database Connectivity)** is a Java API used to connect Java applications with relational databases like PostgreSQL, MySQL, Oracle Database, etc.

It allows us to:

* Establish database connections
* Execute SQL queries
* Retrieve and process results
* Manage transactions

In enterprise applications, JDBC acts as the low-level database communication layer.

---

# JDBC Flow / Steps

A typical JDBC flow contains these steps:

## 1. Load the Driver

Earlier we used:

```java
Class.forName("org.postgresql.Driver");
```

But in modern JDBC (JDBC 4+), the driver gets auto-loaded if the dependency is present.

Example Maven dependency:

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
</dependency>
```

---

## 2. Create Connection

We establish connection using `DriverManager`.

```java
Connection con = DriverManager.getConnection(
    "jdbc:postgresql://localhost:5432/testdb",
    "user",
    "password"
);
```

This creates a session between Java application and database.

---

## 3. Create Statement

There are mainly 3 types:

| Type              | Usage               |
| ----------------- | ------------------- |
| Statement         | Simple static SQL   |
| PreparedStatement | Parameterized query |
| CallableStatement | Stored procedures   |

In real projects, we mostly use `PreparedStatement`.

Example:

```java
PreparedStatement ps =
    con.prepareStatement("SELECT * FROM employee WHERE id = ?");
```

---

## 4. Execute Query

### For SELECT

```java
ResultSet rs = ps.executeQuery();
```

### For INSERT/UPDATE/DELETE

```java
int rows = ps.executeUpdate();
```

---

## 5. Process Result

```java
while(rs.next()) {
    System.out.println(rs.getInt("id"));
    System.out.println(rs.getString("name"));
}
```

`ResultSet` stores tabular data returned from database.

---

## 6. Close Resources

Very important to avoid connection leaks.

Modern approach:

```java
try(
    Connection con = DriverManager.getConnection(...);
    PreparedStatement ps = con.prepareStatement(...);
    ResultSet rs = ps.executeQuery();
) {

}
```

This uses try-with-resources.

---

# Why PreparedStatement is Preferred?

This is an important interviewer expectation.

### Advantages:

* Prevents SQL Injection
* Better performance due to query precompilation
* Cleaner code
* Type-safe parameter binding

Example:

```java
ps.setInt(1, 101);
```

Instead of:

```java
"SELECT * FROM emp WHERE id=" + id
```

---

# Transaction Management in JDBC

By default, JDBC uses auto-commit mode.

```java
con.setAutoCommit(false);
```

Then:

```java
con.commit();
con.rollback();
```

Used when multiple DB operations must succeed together.

Example:

* Debit account
* Credit account

If one fails → rollback entire transaction.

---

# JDBC Architecture

You can explain briefly:

```text
Java Application
       ↓
JDBC API
       ↓
JDBC Driver
       ↓
Database
```

Types of JDBC drivers exist, but Type-4 driver is mostly used nowadays because it’s pure Java and database-specific.

---

# JDBC in Spring Boot

For experienced candidates, interviewer may expect transition to Spring.

In Spring Boot:

* We usually don’t write raw JDBC extensively
* We use:

  * Spring JDBC
  * JPA/Hibernate
  * Spring Data JPA

But internally they still use JDBC.

Spring Boot manages:

* Connection pooling
* Transactions
* Resource closing
* Exception translation

Usually via:

* `HikariCP`
* `JdbcTemplate`
* Hibernate ORM

---

# Real Project Experience Answer

You can say:

> “In my projects, we mainly used Spring Data JPA for ORM operations, but for bulk queries and performance-critical operations we sometimes used JdbcTemplate or native queries because JDBC gives better control and lower overhead.”

This sounds realistic for 5 years experience.

---

# Common Interview Follow-up Questions

## Difference between Statement and PreparedStatement?

| Statement                   | PreparedStatement      |
| --------------------------- | ---------------------- |
| Dynamic query               | Precompiled query      |
| Vulnerable to SQL Injection | Prevents SQL Injection |
| Lower performance           | Better performance     |
| Not parameterized           | Parameterized          |

---

## What is Connection Pooling?

Instead of creating DB connections every time:

* Connections are reused
* Improves performance
* Reduces DB overhead

In Spring Boot:

* `HikariCP` is default pool.

---

## What causes Connection Leak?

When connections are not closed properly.

Effects:

* Application slowdown
* DB exhaustion
* Production outages

---

# Short Crisp Interview Version (2-minute answer)

> JDBC is a Java API used to connect Java applications with relational databases. The typical flow includes loading the driver, establishing a connection using DriverManager, creating PreparedStatement, executing SQL queries, processing ResultSet, and closing resources.
>
> In real projects, we prefer PreparedStatement because it prevents SQL injection and improves performance through query precompilation.
>
> JDBC also supports transaction management using commit and rollback.
>
> In Spring Boot applications, we usually use JPA or JdbcTemplate, but internally they rely on JDBC. Connection pooling is typically managed using HikariCP for better performance.
