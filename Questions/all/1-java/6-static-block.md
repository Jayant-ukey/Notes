# Que: If a staic block throws an exception, what happens to the class loading process?

## Short Answer

> If a static block throws an exception during class initialization, the class initialization fails and the JVM throws an `ExceptionInInitializerError`. The class cannot be used successfully, and subsequent attempts to use it may result in a `NoClassDefFoundError`.

---

## Example

```java
class Test {

    static {
        System.out.println("Static block executed");
        int x = 10 / 0;
    }

    public static void display() {
        System.out.println("Display");
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Test.display();
    }
}
```

### Output

```text
Static block executed
Exception in thread "main"
java.lang.ExceptionInInitializerError
```

---

## Why Does This Happen?

Class lifecycle:

```text
Loading
   ↓
Linking
   ↓
Initialization  ← Static variables & static blocks execute here
```

The static block runs during the **initialization phase**.

If any unchecked exception occurs:

```java
static {
    throw new RuntimeException("Error");
}
```

the JVM wraps it in:

```text
ExceptionInInitializerError
```

because the class failed to initialize properly.

---

## What Happens Afterwards?

Suppose:

```java
public class Demo {

    static {
        throw new RuntimeException("Failed");
    }
}
```

First access:

```java
Demo obj = new Demo();
```

Result:

```text
ExceptionInInitializerError
```

Second access:

```java
Demo obj2 = new Demo();
```

Result:

```text
NoClassDefFoundError
```

because the JVM remembers that class initialization previously failed.

---

## Checked Exception in Static Block?

This won't compile:

```java
static {
    throw new Exception("Error");
}
```

Compiler error because static blocks cannot throw checked exceptions unless handled.

You must catch them:

```java
static {
    try {
        throw new Exception("Error");
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

---

## Real-World Example

Imagine:

```java
class DatabaseConfig {

    static {
        Connection con = DriverManager.getConnection(...);
    }
}
```

If database connection initialization fails during static block execution:

* Class initialization fails
* `ExceptionInInitializerError` occurs
* Application startup may fail

This is one reason why putting heavy initialization logic in static blocks is generally discouraged in enterprise applications.

---

## Interview Answer (30 Seconds)

> Static blocks execute during the class initialization phase. If a static block throws an unchecked exception, the JVM wraps it in an `ExceptionInInitializerError` and class initialization fails. The class becomes unusable, and subsequent attempts to access it can result in a `NoClassDefFoundError`. Checked exceptions cannot be thrown directly from a static block unless they are handled within the block.
