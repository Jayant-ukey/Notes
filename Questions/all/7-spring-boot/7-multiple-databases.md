# Que - How to use 2 separate daabases in spring boot?

### 1. Direct Answer (What)

In Spring Boot, we can use **two separate databases** by configuring **multiple DataSources**, along with separate:

* `DataSource`
* `EntityManagerFactory`
* `TransactionManager`

Each database gets its own configuration so Spring knows which entity/repository belongs to which DB.

👉 In short:

> We achieve multi-database support by defining multiple DataSource configurations and mapping different packages to each database.

---

### 2. Internal Understanding (How it works)

By default, Spring Boot auto-configures **one DataSource**.

For multiple databases:

* You disable auto primary assumption for one datasource
* Define **two configurations**
* Use `@Primary` to mark default DB
* Use package separation for repositories/entities

---

### 3. Example Scenario

Let’s say:

* DB1 → Employee Database
* DB2 → Department Database

---

### 4. Project Structure (important in interviews)

```text id="db1"
com.example.demo
│
├── employee
│     ├── entity
│     ├── repository
│     └── service
│
├── department
│     ├── entity
│     ├── repository
│     └── service
│
├── config
│     ├── EmployeeDBConfig.java
│     └── DepartmentDBConfig.java
```

---

### 5. Configuration of First Database (Employee DB)

```java id="db2"
@Configuration
@EnableJpaRepositories(
        basePackages = "com.example.demo.employee.repository",
        entityManagerFactoryRef = "employeeEntityManager",
        transactionManagerRef = "employeeTransactionManager"
)
public class EmployeeDBConfig {

    @Primary
    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.employee")
    public DataSource employeeDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Primary
    @Bean
    public LocalContainerEntityManagerFactoryBean employeeEntityManager() {
        return new LocalContainerEntityManagerFactoryBean();
    }

    @Primary
    @Bean
    public PlatformTransactionManager employeeTransactionManager() {
        return new JpaTransactionManager();
    }
}
```

---

### 6. Configuration of Second Database (Department DB)

```java id="db3"
@Configuration
@EnableJpaRepositories(
        basePackages = "com.example.demo.department.repository",
        entityManagerFactoryRef = "departmentEntityManager",
        transactionManagerRef = "departmentTransactionManager"
)
public class DepartmentDBConfig {

    @Bean
    @ConfigurationProperties(prefix = "spring.datasource.department")
    public DataSource departmentDataSource() {
        return DataSourceBuilder.create().build();
    }

    @Bean
    public LocalContainerEntityManagerFactoryBean departmentEntityManager() {
        return new LocalContainerEntityManagerFactoryBean();
    }

    @Bean
    public PlatformTransactionManager departmentTransactionManager() {
        return new JpaTransactionManager();
    }
}
```

---

### 7. application.properties

```properties id="db4"
spring.datasource.employee.url=jdbc:mysql://localhost:3306/employee_db
spring.datasource.employee.username=root
spring.datasource.employee.password=pass

spring.datasource.department.url=jdbc:mysql://localhost:3306/department_db
spring.datasource.department.username=root
spring.datasource.department.password=pass
```

---

### 8. How Spring routes repositories (important concept)

Spring uses:

```text id="db5"
@EnableJpaRepositories(basePackages = ...)
```

👉 So:

* Employee repositories → Employee DB
* Department repositories → Department DB

---

### 9. Real-world Usage (Production insight)

Multiple databases are used when:

* Legacy + new system integration
* Microservices sharing external DBs
* Read/write separation (master-slave DBs)
* Data isolation (tenant-based systems)

Example:

* User DB → authentication
* Order DB → transactions

---

### 10. Best Practices / Production Considerations

✔ Always separate packages per DB
✔ Use `@Primary` carefully (only one default DB)
✔ Keep transaction managers isolated
✔ Avoid cross-DB transactions (distributed transactions are complex)
✔ Use proper naming conventions for beans
✔ Prefer microservices instead of multiple DBs in one service when possible

---

### 11. Key Points Interviewers Look For

* Need for multiple DataSources
* Role of `@EnableJpaRepositories`
* Separation of entity/repository packages
* Use of `@Primary`
* Separate EntityManagerFactory + TransactionManager
* Real-world use cases
* Awareness of complexity and trade-offs

---

### 12. Common Follow-up Questions

1. How does Spring decide which DB to use?
2. What is @Primary used for in multiple DB config?
3. Can we use transactions across two databases?
4. What is EntityManagerFactory?
5. Difference between single DB and multi DB setup in Spring Boot?
6. What are the challenges of multiple DataSources?
7. When should we avoid multiple DBs in one service?

---

### One-Line Senior-Level Summary

> "In Spring Boot, multiple databases are configured by defining separate DataSources, EntityManagerFactories, and TransactionManagers for each DB, along with package-based repository separation using @EnableJpaRepositories, allowing each module to interact with its own database independently."
