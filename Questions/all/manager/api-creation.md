# Que- Implement Employee CRUD APIs with proper layers (Controller, Service, Repository, Entity) and exception handling.


A strong 4-year candidate should be able to write something like this in 15–20 minutes. The expected architecture is Controller → Service → Repository, with business logic in Service and database access in Repository. Exception handling should be centralized using `@RestControllerAdvice` or `@ControllerAdvice`. ([Spring Bootのイロハ][1])

---

# Project Structure

```text
com.example.employee

├── controller
│   └── EmployeeController
├── service
│   └── EmployeeService
├── repository
│   └── EmployeeRepository
├── entity
│   └── Employee
├── exception
│   ├── ResourceNotFoundException
│   └── GlobalExceptionHandler
```

---

# 1. Entity

```java
@Entity
@Table(name = "employees")
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    private String email;

    private Double salary;

    // getters setters
}
```

---

# 2. Repository

```java
@Repository
public interface EmployeeRepository
        extends JpaRepository<Employee, Long> {

}
```

I expect the candidate to know that extending `JpaRepository` automatically provides CRUD methods such as `save()`, `findById()`, `findAll()`, and `deleteById()`. ([Spring Bootのイロハ][1])

---

# 3. Service

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeRepository repository;

    public Employee createEmployee(Employee employee) {
        return repository.save(employee);
    }

    public List<Employee> getAllEmployees() {
        return repository.findAll();
    }

    public Employee getEmployeeById(Long id) {

        return repository.findById(id)
                .orElseThrow(() ->
                        new ResourceNotFoundException(
                                "Employee not found with id " + id));
    }

    public Employee updateEmployee(Long id,
                                   Employee employee) {

        Employee existing =
                getEmployeeById(id);

        existing.setName(employee.getName());
        existing.setEmail(employee.getEmail());
        existing.setSalary(employee.getSalary());

        return repository.save(existing);
    }

    public void deleteEmployee(Long id) {

        Employee employee =
                getEmployeeById(id);

        repository.delete(employee);
    }
}
```

---

# 4. Custom Exception

```java
public class ResourceNotFoundException
        extends RuntimeException {

    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

---

# 5. Global Exception Handler

This is where many candidates lose marks.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<String> handleNotFound(
            ResourceNotFoundException ex) {

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleException(
            Exception ex) {

        return ResponseEntity
                .status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("Something went wrong");
    }
}
```

Centralized exception handling through `@RestControllerAdvice` is generally preferred because controllers remain thin and business exceptions can be thrown from the service layer. ([GeeksforGeeks][2])

---

# 6. Controller

```java
@RestController
@RequestMapping("/employees")
public class EmployeeController {

    @Autowired
    private EmployeeService service;

    @PostMapping
    public ResponseEntity<Employee>
    createEmployee(@RequestBody Employee employee) {

        return ResponseEntity.ok(
                service.createEmployee(employee));
    }

    @GetMapping
    public ResponseEntity<List<Employee>>
    getAllEmployees() {

        return ResponseEntity.ok(
                service.getAllEmployees());
    }

    @GetMapping("/{id}")
    public ResponseEntity<Employee>
    getEmployee(@PathVariable Long id) {

        return ResponseEntity.ok(
                service.getEmployeeById(id));
    }

    @PutMapping("/{id}")
    public ResponseEntity<Employee>
    updateEmployee(@PathVariable Long id,
                   @RequestBody Employee employee) {

        return ResponseEntity.ok(
                service.updateEmployee(id, employee));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<String>
    deleteEmployee(@PathVariable Long id) {

        service.deleteEmployee(id);

        return ResponseEntity.ok(
                "Employee deleted successfully");
    }
}
```

---

# What Would Make Me Say "Strong Candidate"?

If after writing the above, you add:

### DTOs

```java
EmployeeRequestDTO
EmployeeResponseDTO
```

and say:

> "In production I won't expose Entity directly from Controller. I'll use DTOs."

That is a very good answer. DTO separation is widely considered a better production design. ([Spring Bootのイロハ][3])

---

### Validation

```java
public class EmployeeRequest {

    @NotBlank
    private String name;

    @Email
    private String email;
}
```

```java
@PostMapping
public Employee create(
        @Valid @RequestBody EmployeeRequest request)
```

---

### Constructor Injection

Instead of:

```java
@Autowired
private EmployeeService service;
```

use:

```java
private final EmployeeService service;

public EmployeeController(EmployeeService service) {
    this.service = service;
}
```

---

### Transaction

```java
@Transactional
public Employee updateEmployee(...)
```

---

### Follow-up Questions I Would Ask

After you finish coding, I'd immediately ask:

1. Why Service layer?
2. Why not call Repository directly from Controller?
3. Why RuntimeException?
4. Difference between PUT and PATCH?
5. Why DTO?
6. What happens if database is down?
7. How would you paginate GET /employees?
8. How would you add Swagger?
9. How would you secure APIs using JWT?
10. How would you implement soft delete?

For a Java developer with 4 years of experience, being able to write the CRUD code above **plus answer those 10 follow-ups confidently** is usually enough to clear the technical portion of a techno-managerial round.

[1]: https://www.springboot-123.com/en/blog/spring-boot-rest-api-crud-tutorial/?utm_source=chatgpt.com "Implementing CRUD REST API with Spring Boot - Basic Structure of Controller, Service, and Repository | Spring Boot 123"
[2]: https://www.geeksforgeeks.org/exception-handling-in-spring-boot/?utm_source=chatgpt.com "Exception Handling in Spring Boot - GeeksforGeeks"
[3]: https://springboot-123.mizucoffee.com/en/blog/spring-boot-rest-api-crud-tutorial/?utm_source=chatgpt.com "Implementing REST API CRUD with Spring Boot - Basic Structure of Controller, Service, and Repository | Spring Boot 123"
