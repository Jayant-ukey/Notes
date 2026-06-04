# Que : How do you handle exceptions in Java?

In a **5-year experienced Java/Spring Boot interview**, this question is not just about syntax. Interviewers expect:

* Types of exceptions
* Handling strategies
* Best practices in real applications
* Spring Boot exception handling approach

---

# How do you handle exceptions in Java?

Exception handling in Java is done using a combination of:

* `try-catch-finally`
* `throw` / `throws`
* Custom exceptions
* Global exception handling (in Spring Boot)

---

# 1. Try-Catch-Finally

## Basic Structure

```java id="a1b2c3"
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
} finally {
    System.out.println("Always executed");
}
```

---

## Key Points

* `try` → risky code
* `catch` → handles exception
* `finally` → always executes (cleanup)

---

# 2. Multiple Catch Blocks

```java id="d4e5f6"
try {
    String str = null;
    str.length();
} catch (ArithmeticException e) {
    System.out.println("Arithmetic issue");
} catch (NullPointerException e) {
    System.out.println("Null pointer issue");
}
```

---

# 3. Multi-Catch (Java 7+)

```java id="x1y2z3"
try {
    // code
} catch (IOException | SQLException e) {
    e.printStackTrace();
}
```

---

# 4. throw vs throws

## throw (manually throwing exception)

```java id="m4n5o6"
throw new RuntimeException("Invalid input");
```

---

## throws (declaring exception)

```java id="p7q8r9"
public void readFile() throws IOException {
}
```

---

# 5. Checked vs Unchecked Exceptions

## Checked Exception (Compile-time)

Must be handled or declared.

Examples:

* IOException
* SQLException

---

## Unchecked Exception (Runtime)

Examples:

* NullPointerException
* ArithmeticException

---

# 6. Custom Exceptions (Important in Real Projects)

```java id="z1a2b3"
class InsufficientBalanceException extends RuntimeException {

    public InsufficientBalanceException(String message) {
        super(message);
    }
}
```

Usage:

```java id="c4d5e6"
if(balance < amount) {
    throw new InsufficientBalanceException("Insufficient funds");
}
```

---

# 7. Finally vs return behavior

```java id="f7g8h9"
public int test() {
    try {
        return 1;
    } finally {
        System.out.println("Finally executes even after return");
    }
}
```

---

# 8. Exception Handling in Spring Boot (VERY IMPORTANT)

In real enterprise apps, we don't use try-catch everywhere.

We use global handling:

Spring Boot

---

## @ExceptionHandler

```java id="s1t2u3"
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public ResponseEntity<String> handleRuntime(RuntimeException ex) {
        return ResponseEntity.badRequest().body(ex.getMessage());
    }
}
```

---

## Custom Exception Handling

```java id="v4w5x6"
@ExceptionHandler(InsufficientBalanceException.class)
public ResponseEntity<String> handleBalance(InsufficientBalanceException ex) {
    return ResponseEntity.badRequest().body(ex.getMessage());
}
```

---

## Standard Error Response (Best Practice)

```json id="y7z8a9"
{
  "timestamp": "2026-06-04",
  "message": "Insufficient balance",
  "status": 400
}
```

---

# 9. Best Practices (Very Important for 5 Years Exp)

## 1. Don’t swallow exceptions

❌ Bad:

```java
catch(Exception e) {}
```

---

## 2. Use specific exceptions

Instead of generic `Exception`.

---

## 3. Use custom exceptions for business logic

Example:

* UserNotFoundException
* PaymentFailedException

---

## 4. Centralized exception handling in Spring Boot

Avoid repetitive try-catch in controllers.

---

## 5. Log exceptions properly

Use logging frameworks like:

* Logback
* SLF4J

---

# Real Project Answer (Interview Ready)

> In Java, exceptions are handled using try-catch-finally blocks for local handling and throw/throws for propagating exceptions. We distinguish between checked and unchecked exceptions and create custom exceptions for business logic.
>
> In Spring Boot applications, we generally avoid handling exceptions in every layer. Instead, we use @RestControllerAdvice with @ExceptionHandler for global exception handling, which ensures consistent error responses across microservices.
>
> We also follow best practices like using specific exceptions, proper logging, and avoiding empty catch blocks.

---

# Common Interview Follow-ups

## Q1: What happens if exception is not caught?

Program terminates abnormally.

---

## Q2: Can finally block be skipped?

Rare cases:

* System.exit()
* JVM crash

---

## Q3: Why use custom exceptions?

To represent business-specific errors clearly.

---

## Q4: Difference between Error and Exception?

| Error              | Exception               |
| ------------------ | ----------------------- |
| System-level issue | Application-level issue |
| Cannot recover     | Can handle              |
| OutOfMemoryError   | NullPointerException    |

---

# Short Crisp Interview Answer

> Exception handling in Java is done using try-catch-finally blocks. Checked exceptions must be handled or declared using throws, while unchecked exceptions occur at runtime.
>
> We can also create custom exceptions for business logic. In Spring Boot, we use @RestControllerAdvice for global exception handling instead of handling exceptions in every method, which ensures consistent error responses across the application.
