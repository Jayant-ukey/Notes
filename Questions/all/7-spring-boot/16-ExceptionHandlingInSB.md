# Que - How do you handle exception in spring boot and which annotations used for exceptions?

## ✅ Interview-ready answer

In Spring Boot, I handle exceptions using a **centralized exception handling approach**, so that the API does not expose stack traces and we return consistent, meaningful error responses to clients.

Typically, I use **`@ControllerAdvice` + `@ExceptionHandler`** for global exception handling.

---

## 📌 How I explain it in an interview

In a production Spring Boot application, instead of handling exceptions in every controller, I create a **global exception handler class** that intercepts exceptions thrown from any layer (controller/service/repository) and converts them into standardized HTTP responses.

---

## ⚙️ 1. Global Exception Handler

### `@ControllerAdvice` + `@ExceptionHandler`

```java id="e1"
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<String> handleResourceNotFound(ResourceNotFoundException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGenericException(Exception ex) {
        return new ResponseEntity<>("Something went wrong", HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

---

## ⚙️ 2. Custom Exception

I usually create custom exceptions for business logic:

```java id="e2"
public class ResourceNotFoundException extends RuntimeException {

    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

---

## ⚙️ 3. Throwing exception from service layer

```java id="e3"
public Employee getEmployee(Long id) {
    return repository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Employee not found with id: " + id));
}
```

---

## ⚙️ 4. Standard error response (best practice)

In real projects, instead of returning plain strings, I return a structured response:

```java id="e4"
public class ErrorResponse {
    private String message;
    private int status;
    private LocalDateTime timestamp;
}
```

---

## 🔑 Key annotations used for exception handling

### 1. `@ControllerAdvice`

* Used for **global exception handling across controllers**

### 2. `@RestControllerAdvice`

* Combination of `@ControllerAdvice + @ResponseBody`
* Returns JSON responses directly

### 3. `@ExceptionHandler`

* Defines method to handle specific exception types

---

## ⭐ Key points interviewers look for

* Global exception handling is preferred over try-catch everywhere
* Separation of concerns (controller should not handle business exceptions)
* Custom exceptions improve clarity and maintainability
* Consistent API error response structure
* Proper HTTP status codes (404, 400, 500, etc.)

---

## 🚀 Production-level insights

* Always return **structured error responses**, not plain text
* Use proper HTTP status codes:

  * 400 → Bad Request
  * 404 → Not Found
  * 500 → Internal Server Error
* Log exceptions properly using SLF4J
* Avoid exposing internal stack traces in production
* Use validation exceptions (`MethodArgumentNotValidException`) for request validation
* In microservices, propagate meaningful error codes across services

---

## 🔁 Common follow-up questions

1. Difference between `@ControllerAdvice` and `@RestControllerAdvice`?
2. What happens if no `@ExceptionHandler` is defined?
3. How do you handle validation exceptions in Spring Boot?
4. What is the difference between checked and unchecked exceptions in Spring?
5. How do you log exceptions in production systems?
6. Can we have multiple `@ControllerAdvice` classes?

---

If you want next, I can simulate a **real interview drill: tricky exception handling scenario (validation + service + global handler flow)** which is very commonly asked in 5-year Spring Boot interviews.
