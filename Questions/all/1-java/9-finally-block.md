# Que: Apart from try-catch, there is also a finally block. Do you know about it?

Yes — and for a **5-year experienced candidate**, you should explain it with **behavior, use cases, and edge cases**, not just definition.

---

# What is `finally` block in Java?

The `finally` block is used to execute **important cleanup code** that must run **whether an exception occurs or not**.

It is always associated with `try-catch`.

---

## Basic Syntax

```java id="a1b2c3"
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Exception handled");
} finally {
    System.out.println("Finally block executed");
}
```

---

## Key Behavior

> `finally` block always executes **after try-catch execution**, regardless of:

* Exception occurs or not
* Exception is handled or not
* return statement in try or catch

---

# When is `finally` used?

Mainly for **resource cleanup**:

* Closing database connections
* Closing file streams
* Releasing locks
* Cleaning up resources

---

## Example (File Handling)

```java id="d4e5f6"
FileReader fr = null;

try {
    fr = new FileReader("test.txt");
    int data = fr.read();
} catch (Exception e) {
    e.printStackTrace();
} finally {
    try {
        if (fr != null)
            fr.close();
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

---

# Important Interview Points

## 1. finally executes even after return

```java id="x1y2z3"
public int test() {
    try {
        return 10;
    } finally {
        System.out.println("Finally executed");
    }
}
```

### Output:

```text id="m4n5o6"
Finally executed
```

---

## 2. finally vs System.exit()

Finally will NOT execute if JVM shuts down:

```java id="p7q8r9"
try {
    System.exit(0);
} finally {
    System.out.println("Will not execute");
}
```

---

## 3. finally with exception not handled

```java id="z1a2b3"
try {
    int x = 10 / 0;
} finally {
    System.out.println("Finally runs even if exception is not caught");
}
```

---

## 4. finally vs try-with-resources (Modern Java)

In modern Java (Java 7+), finally is often replaced by:

```java id="c4d5e6"
try (FileReader fr = new FileReader("test.txt")) {
    fr.read();
}
```

This automatically handles cleanup.

Used heavily in:
Java SE 7

---

# Common Misconceptions

## ❌ finally is optional only

Actually:

* try must be followed by catch OR finally
* or both

Valid:

```java
try {} catch {} finally {}
try {} finally {}
```

Invalid:

```java
try {}
```

---

## ❌ finally does NOT guarantee execution in all cases

It does NOT execute when:

* JVM crashes
* System.exit() called
* Power failure / abrupt termination

---

# Real-World Example (Spring Boot Context)

In Spring Boot:

Earlier (non-Spring-managed JDBC):

```java id="f7g8h9"
Connection con = null;

try {
    con = dataSource.getConnection();
} finally {
    if (con != null) con.close();
}
```

Now replaced by:

* Connection pooling (HikariCP)
* try-with-resources
* Spring transaction management

---

# Key Interview Answer (Expected 5-year level)

> The finally block in Java is used to execute important cleanup code such as closing resources like database connections or file streams. It always executes after the try-catch block, regardless of whether an exception occurs or not, even if a return statement is present in try or catch.
>
> However, finally will not execute in cases like JVM shutdown using System.exit() or system crashes. In modern Java, finally is often replaced by try-with-resources for automatic resource management, especially for IO and database operations.

---

# One-Line Answer

> finally is a block that always executes after try-catch and is mainly used for cleanup operations like closing resources, regardless of whether an exception occurs or not.
