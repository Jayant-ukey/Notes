# Que: What are the different types of exceptions in Java?

In a **5-year experienced Java interview**, this question is expected to be answered in a structured way with:

* Classification
* Examples
* Runtime behavior
* Checked vs unchecked clarity
* Real-world relevance

---

# Types of Exceptions in Java

In Java, exceptions are broadly classified into **3 categories**:

```text id="a1b2c3"
Throwable
 ├── Error
 └── Exception
       ├── Checked Exception
       └── Unchecked Exception (RuntimeException)
```

---

# 1. Checked Exceptions

## What are they?

Checked exceptions are those that are **checked at compile time**.

👉 You must handle them using:

* try-catch
* or throws keyword

---

## Examples

* IOException
* SQLException
* FileNotFoundException
* ClassNotFoundException

---

## Example

```java id="x1y2z3"
import java.io.*;

public class Test {
    public static void main(String[] args) throws IOException {
        FileReader fr = new FileReader("test.txt");
    }
}
```

---

## Key Point

> If not handled, code will NOT compile.

---

## Real-world example

* Reading/writing files
* Database connection
* Network calls

---

# 2. Unchecked Exceptions (Runtime Exceptions)

## What are they?

These exceptions occur at **runtime** and are NOT checked at compile time.

They extend:

```text id="m4n5o6"
RuntimeException
```

---

## Examples

* NullPointerException
* ArithmeticException
* ArrayIndexOutOfBoundsException
* IllegalArgumentException

---

## Example

```java id="p7q8r9"
public class Test {
    public static void main(String[] args) {
        int a = 10 / 0; // ArithmeticException
    }
}
```

---

## Key Point

> These are programming bugs, not external issues.

---

## Real-world example

* Null object access
* Invalid input validation
* Array index errors

---

# 3. Errors (Not Exceptions)

## What are they?

Errors are serious problems that are **not meant to be handled by application code**.

They occur at JVM/system level.

---

## Examples

* OutOfMemoryError
* StackOverflowError
* VirtualMachineError

---

## Example

```java id="z1a2b3"
public class Test {
    public static void main(String[] args) {
        recursiveMethod();
    }

    static void recursiveMethod() {
        recursiveMethod(); // StackOverflowError
    }
}
```

---

## Key Point

> Errors are generally unrecoverable.

---

# Comparison Table

| Type                | Checked at Compile Time | Handling Required | Examples                  |
| ------------------- | ----------------------- | ----------------- | ------------------------- |
| Checked Exception   | Yes                     | Yes               | IOException, SQLException |
| Unchecked Exception | No                      | Optional          | NullPointerException      |
| Error               | No                      | Not expected      | OutOfMemoryError          |

---

# Important Interview Insight

## 1. Why two types of exceptions?

Java separates them based on:

* Recoverable conditions → Checked exceptions
* Programming mistakes → Unchecked exceptions

---

## 2. Why RuntimeException is unchecked?

Because:

> It represents developer mistakes, not external failures.

---

## 3. Should we handle RuntimeExceptions?

Best practice:

* Don’t blindly catch them
* Fix root cause
* Use validation instead

---

# Real-World Spring Boot Context

In Spring Boot applications:

* Checked exceptions → external systems (DB, file, API)
* Runtime exceptions → business logic errors
* Errors → infrastructure failures

We usually:

* Handle exceptions globally using `@RestControllerAdvice`

---

# Real Project Answer (Interview Ready)

> In Java, exceptions are classified into checked exceptions, unchecked exceptions, and errors. Checked exceptions are checked at compile time and must be handled, such as IOException and SQLException. Unchecked exceptions occur at runtime and represent programming errors like NullPointerException and ArithmeticException. Errors are severe system-level issues like OutOfMemoryError which are not meant to be handled by application code.
>
> In enterprise applications like Spring Boot, we typically handle runtime and business exceptions using global exception handlers while checked exceptions are used for external system interactions like database or file operations.

---

# Common Follow-up Questions

## Q1: Can we catch Error?

Yes, but not recommended.

---

## Q2: Difference between Exception and Error?

| Exception         | Error                 |
| ----------------- | --------------------- |
| Recoverable       | Not recoverable       |
| Application-level | JVM-level             |
| Can be handled    | Should not be handled |

---

## Q3: Why NullPointerException is unchecked?

Because it represents a bug in code, not external dependency failure.

---

# Short Crisp Interview Answer

> Java exceptions are classified into checked exceptions, unchecked exceptions, and errors. Checked exceptions are handled at compile time, such as IOException. Unchecked exceptions occur at runtime like NullPointerException and represent programming errors. Errors are serious system-level issues like OutOfMemoryError and are not meant to be handled by applications.
