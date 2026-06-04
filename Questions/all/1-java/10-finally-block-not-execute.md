# Que: You mentioned that the finally block is always executed. Are there situations where it may not execute? If yes, how?

Yes — **“finally always executes” is a rule with a few important exceptions**. In interviews, a 5-year experienced answer should clearly explain **edge cases where JVM bypasses finally**.

---

# When `finally` may NOT execute

Although `finally` is designed to always run, there are a few scenarios where it can be skipped due to **JVM termination or thread interruption at a lower level**.

---

# 1. JVM Shutdown using `System.exit()`

If the program explicitly terminates the JVM, `finally` will not execute.

```java id="a1b2c3"
public class Test {
    public static void main(String[] args) {

        try {
            System.out.println("Try block");
            System.exit(0);
        } finally {
            System.out.println("Finally block");
        }
    }
}
```

### Output:

```
Try block
```

### Why?

* `System.exit(0)` immediately stops the JVM
* No further code (including finally) runs

---

# 2. JVM Crash / Fatal Error

If the JVM crashes due to a critical error:

* Segmentation fault (rare in Java, but possible via native code)
* JVM bug
* Native library crash (JNI)

Then `finally` will not execute.

Example scenario:

* Using unsafe native code
* JVM memory corruption

---

# 3. Power Failure / System Shutdown

If the system abruptly shuts down:

* Power cut
* OS crash
* Hardware failure

Then JVM does not get a chance to execute `finally`.

---

# 4. Infinite Loop or Thread Termination Before Reaching finally

If the thread never reaches the `finally` block:

```java id="d4e5f6"
try {
    while(true) {
        // infinite loop
    }
} finally {
    System.out.println("Finally");
}
```

### Why not executed?

* Control never exits `try`

---

# 5. Thread killed using `stop()` (Deprecated)

```java id="x1y2z3"
Thread t = new Thread(() -> {
    try {
        System.out.println("Running");
    } finally {
        System.out.println("Finally");
    }
});

t.start();
t.stop(); // deprecated
```

### Why finally may not execute properly?

* Thread is forcefully killed
* JVM does not guarantee cleanup

---

# 6. Daemon Thread Termination

If a JVM exits while only daemon threads are running:

* JVM stops immediately
* `finally` may not execute

---

# Important Interview Insight

👉 The correct statement is:

> `finally` is guaranteed to execute **as long as the JVM and thread are still alive and execution reaches the try block normally.**

---

# What Interviewers Expect You to Say

A strong answer:

> Yes, finally generally always executes, but there are exceptions. It will not execute if the JVM is terminated explicitly using System.exit(), if there is a JVM crash or fatal error, or if the system shuts down abruptly like a power failure. It may also not execute if the thread is forcefully stopped or never reaches the finally block due to infinite loops.

---

# Real-World Example (Important Perspective)

In enterprise apps like Spring Boot:

* We rarely rely on finally for cleanup anymore
* Instead we use:

  * try-with-resources
  * connection pooling (HikariCP)
  * transaction management

So risk of finally not executing is mostly theoretical in modern systems.

---

# One-Line Interview Answer

> Finally usually always executes, but it may not run if the JVM is terminated using System.exit(), a fatal JVM crash occurs, a system power failure happens, or the thread never reaches the finally block due to infinite loops or forced thread termination.
