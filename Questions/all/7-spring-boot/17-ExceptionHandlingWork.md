# Que - How does exception handling work in Spring Boot REST APIs?

## ✅ Interview-ready answer

In Spring Boot REST APIs, exception handling works through a **combination of controller-level exceptions, service-layer propagation, and centralized global exception handling**. The main idea is to ensure that exceptions are **not exposed directly to the client**, and instead converted into **consistent HTTP responses (JSON format + proper status codes).**

---

## 📌 How I explain it in an interview

When an exception occurs in a REST API, it typically flows from:

**Controller → Service → Repository → throws exception → Global Exception Handler intercepts → returns structured HTTP response**

Instead of handling exceptions everywhere, we centralize it using Spring’s exception handling mechanism.

---

## ⚙️ 1. Exception propagation flow

Example scenario:

```java id="e1"
public Employee getEmployee(Long id) {
    return repository.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Employee not found"));
}
```

If employee is not found:

* Exception is thrown from service layer
* Propagates to controller
* Handled by global handler

---

## ⚙️ 2. Global Exception Handling (core mechanism)

### Using `@RestControllerAdvice`

```java id="e2"
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFound(ResourceNotFoundException ex) {

        ErrorResponse error = new ErrorResponse(
                ex.getMessage(),
                HttpStatus.NOT_FOUND.value(),
                LocalDateTime.now()
        );

        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }
}
```

---

## ⚙️ 3. Custom exception creation

```java id="e3"
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

---

## ⚙️ 4. Default Spring Boot exception handling

If we don’t define custom handlers, Spring Boot uses:

* `DefaultErrorAttributes`
* `BasicErrorController`

It returns a default JSON response like:

```json
{
  "timestamp": "...",
  "status": 500,
  "error": "Internal Server Error",
  "message": "..."
}
```

But this is **not suitable for production APIs**, so we override it.

---

## ⚙️ 5. Validation exception handling (important in REST APIs)

When using `@Valid`:

```java id="e4"
@PostMapping("/employees")
public Employee create(@Valid @RequestBody Employee emp) {
    return service.save(emp);
}
```

We handle validation errors like this:

```java id="e5"
@ExceptionHandler(MethodArgumentNotValidException.class)
public ResponseEntity<Map<String, String>> handleValidation(MethodArgumentNotValidException ex) {

    Map<String, String> errors = new HashMap<>();

    ex.getBindingResult().getFieldErrors().forEach(error ->
        errors.put(error.getField(), error.getDefaultMessage())
    );

    return new ResponseEntity<>(errors, HttpStatus.BAD_REQUEST);
}
```

---

## 🔑 Key annotations used

* `@RestControllerAdvice` → global exception handler for REST APIs
* `@ExceptionHandler` → handles specific exceptions
* `@ResponseStatus` (alternative approach) → sets HTTP status on exception class
* `@Valid` → triggers validation exceptions

---

## ⭐ Key points interviewers look for

* Centralized exception handling using `@RestControllerAdvice`
* Separation of concerns (no try-catch everywhere)
* Mapping exceptions to proper HTTP status codes
* Custom exception classes for business logic
* Consistent error response structure (important in REST APIs)
* Handling validation errors separately

---

## 🚀 Production-level insights

* Always return **structured error response DTO**
* Avoid exposing stack traces in responses
* Log exceptions using SLF4J for debugging
* Standardize error format across all microservices
* Use correlation IDs for tracing in distributed systems
* Differentiate between:

  * Client errors (400, 404)
  * Server errors (500)
* In microservices, propagate meaningful error codes/messages

---

## 🔁 Common follow-up questions

1. What is the difference between `@ControllerAdvice` and `@RestControllerAdvice`?
2. How does Spring Boot handle exceptions internally by default?
3. What happens if multiple `@ExceptionHandler` methods match?
4. How do you handle validation errors globally?
5. Difference between checked and unchecked exceptions in Spring Boot?
6. How do microservices propagate exceptions across services?

---

If you want next, I can give you a **real interview scenario question** like:
👉 *“What happens if an exception is thrown inside a transactional service method?”* (very commonly asked at 5-year level)
