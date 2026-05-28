
# How to Perform Database Connectivity in Java?

## Basic Definition

Database connectivity in Java is the process of establishing communication between a Java application and a relational database using JDBC or ORM frameworks like Hibernate/JPA.

In enterprise applications, database connectivity is typically handled through:

* JDBC
* Spring Data JPA
* Hibernate
* Connection pools like HikariCP

---

# 1. Using JDBC (Core Java Approach)

## Step 1 — Add Database Driver

Example for PostgreSQL:

```xml id="r6f2ih"
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
</dependency>
```

---

## Step 2 — Create Connection

```java id="7n5v5t"
Connection con = DriverManager.getConnection(
    "jdbc:postgresql://localhost:5432/testdb",
    "postgres",
    "password"
);
```

---

## Step 3 — Create Statement

Preferred approach:

```java id="5c7x3d"
PreparedStatement ps =
    con.prepareStatement("SELECT * FROM employee WHERE id=?");
```

---

## Step 4 — Execute Query

```java id="z38x8z"
ps.setInt(1, 101);

ResultSet rs = ps.executeQuery();
```

---

## Step 5 — Process Result

```java id="7fzq6p"
while(rs.next()) {
    System.out.println(rs.getString("name"));
}
```

---

## Step 6 — Close Resources

```java id="kq7oqx"
rs.close();
ps.close();
con.close();
```

Or better:

* try-with-resources

---

# Complete JDBC Example

```java id="4rl6z6"
import java.sql.*;

public class JdbcDemo {

    public static void main(String[] args) {

        String url = "jdbc:postgresql://localhost:5432/testdb";
        String username = "postgres";
        String password = "password";

        try (
            Connection con = DriverManager.getConnection(url, username, password);
            PreparedStatement ps =
                con.prepareStatement("SELECT * FROM employee WHERE id=?")
        ) {

            ps.setInt(1, 1);

            ResultSet rs = ps.executeQuery();

            while(rs.next()) {
                System.out.println(rs.getString("name"));
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

# 2. Database Connectivity in Spring Boot (Most Important for Experienced Candidates)

In real projects, we usually don’t manually create JDBC connections.

Spring Boot automatically manages:

* Connections
* Pooling
* Transactions
* ORM mapping

---

# Step 1 — Add Dependencies

```xml id="axdz9d"
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
</dependency>
```

---

# Step 2 — Configure application.properties

```properties id="n7wgo7"
spring.datasource.url=jdbc:postgresql://localhost:5432/testdb
spring.datasource.username=postgres
spring.datasource.password=password

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

# Step 3 — Create Entity

```java id="gl9q0u"
@Entity
public class Employee {

    @Id
    private Long id;

    private String name;
}
```

---

# Step 4 — Create Repository

```java id="5p3qzv"
public interface EmployeeRepository
       extends JpaRepository<Employee, Long> {
}
```

---

# Step 5 — Use Repository

```java id="5g17gh"
@Autowired
private EmployeeRepository repository;

public Employee getEmployee(Long id) {
    return repository.findById(id).orElse(null);
}
```

---

# What Happens Internally?

Spring Boot:

* Creates DataSource
* Manages connection pool
* Uses Hibernate ORM
* Converts objects ↔ database rows
* Handles transactions

Internally Hibernate still uses JDBC.

---

# Connection Pooling (Very Important)

Creating DB connection every request is expensive.

So Spring Boot uses:

* HikariCP by default

Benefits:

* Reuses connections
* Better performance
* Reduced DB load

---

# Transaction Management

Handled using:

```java id="9ksv8g"
@Transactional
```

Example:

```java id="dlz8ix"
@Transactional
public void transferMoney() {

}
```

Ensures:

* Commit
* Rollback
* ACID behavior

---

# Best Practices (Expected from 5 Years Experience)

## Use PreparedStatement

Avoid SQL Injection.

---

## Use Connection Pooling

Never create manual connections repeatedly.

---

## Externalize Configurations

Use:

* application.properties
* environment variables
* config servers

---

## Proper Exception Handling

Handle:

* SQL exceptions
* Timeouts
* Connection failures

---

## Use ORM Carefully

Avoid:

* N+1 query problem
* unnecessary lazy loading

---

# Real Project Answer

You can say:

> “In our Spring Boot microservices, database connectivity was managed using Spring Data JPA with PostgreSQL. Spring Boot handled connection pooling using HikariCP. For performance-critical queries, we sometimes used native SQL or JdbcTemplate. Transactions were managed using @Transactional annotations.”

This sounds production-level.

---

# Common Follow-up Questions

## What is JDBC URL?

Example:

```text id="v1icmb"
jdbc:postgresql://host:port/database
```

---

## Why HikariCP?

* Lightweight
* High performance
* Fast connection management

---

## Difference between JPA and JDBC?

| JDBC          | JPA                |
| ------------- | ------------------ |
| Low-level API | ORM framework      |
| Manual SQL    | Entity-based       |
| More control  | Faster development |

---

## What happens if connection pool exhausts?

Requests wait until connection becomes available or timeout occurs.

---

# Short Crisp Interview Answer

> Database connectivity in Java is typically achieved using JDBC, where we establish a connection using DriverManager, create PreparedStatement, execute SQL queries, process ResultSet, and close resources properly.
>
> In Spring Boot applications, we usually use Spring Data JPA or Hibernate for ORM-based database interaction. Database configuration is done in application.properties, and connection pooling is managed using HikariCP.
>
> Internally, Hibernate still uses JDBC to communicate with the database. Transactions are typically handled using @Transactional annotations.
