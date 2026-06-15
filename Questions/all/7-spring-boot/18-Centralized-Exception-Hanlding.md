# Que - If you want to return a custom error message with an HTTP status code for specific exceptions, how would you implement centralized exception handling?

## ✅ Interview-ready answer

To return **custom error messages with specific HTTP status codes in a centralized way**, I use Spring Boot’s **global exception handling mechanism** with `@RestControllerAdvice` and `@ExceptionHandler`.

This ensures all exceptions across the application are handled in one place and responses are consistent.

---

## 📌 How I explain it in an interview

I would create a **global exception handler class** that captures specific exceptions and maps them to:

* Custom error message
* Proper HTTP status code
* Standard error response structure

---

## ⚙️ 1. Create a standard error response model

First, I define a reusable error response structure:

```java id="e1"
public class ErrorResponse {

    private String message;
    private int status;
    private LocalDateTime timestamp;

    public ErrorResponse(String message, int status, LocalDateTime timestamp) {
        this.message = message;
        this.status = status;
        this.timestamp = timestamp;
    }

    // getters and setters
}
```

---

## ⚙️ 2. Create custom exception

```java id="e2"
public class ResourceNotFoundException extends RuntimeException {

    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

---

## ⚙️ 3. Centralized exception handler

This is the core part using `@RestControllerAdvice`:

```java id="e3"
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

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGenericException(Exception ex) {

        ErrorResponse error = new ErrorResponse(
                "Internal Server Error",
                HttpStatus.INTERNAL_SERVER_ERROR.value(),
                LocalDateTime.now()
        );

        return new ResponseEntity<>(error, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

---

## ⚙️ 4. Example usage in service layer

```java id="e4"
public Employee getEmployee(Long id) {
    return repository.findById(id)
            .orElseThrow(() ->
                new ResourceNotFoundException("Employee not found with id: " + id));
}
```

---

## 🔑 Key annotations used

* `@RestControllerAdvice` → global exception handling across REST APIs
* `@ExceptionHandler` → maps specific exceptions to handler methods
* `ResponseEntity` → controls HTTP status + response body

---

## ⭐ Key points interviewers look for

* Centralized exception handling instead of try-catch everywhere
* Mapping specific exceptions to specific HTTP status codes
* Consistent error response structure across APIs
* Separation of concerns (controller/service not handling formatting)
* Use of `ResponseEntity` for full HTTP control

---

## 🚀 Production-level insights

* Always use a **standard error DTO** across all microservices
* Avoid exposing internal exception details to clients
* Use meaningful HTTP status codes:

  * `404` → Resource not found
  * `400` → Bad request
  * `500` → Server error
* Log exceptions internally using SLF4J
* Extend handler for validation:

  * `MethodArgumentNotValidException`
* In microservices, include:

  * `errorCode`
  * `traceId` (for debugging distributed systems)

---

## 🔁 Common follow-up questions

1. Why use `@RestControllerAdvice` instead of handling exceptions in controllers?
2. What happens if multiple exception handlers match the same exception?
3. Can we return different response formats for different exceptions?
4. How do you handle validation exceptions globally?
5. What is the difference between `@ResponseStatus` and `ResponseEntity`?
6. How do you implement error handling in microservices consistently?

---

If you want next, I can give you a **real-world senior-level follow-up question:**
👉 *“How would you design a global error handling strategy across multiple microservices?”*
