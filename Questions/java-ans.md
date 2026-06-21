
## 1. Why is OOPs important?

OOP (Object-Oriented Programming) matters because it helps manage complexity in large software systems. Key reasons:

- **Modularity** – Code is organized into objects/classes, making it easier to understand, develop, and maintain.
- **Reusability** – Through inheritance, existing code can be reused instead of rewritten.
- **Encapsulation** – Data and behavior are bundled together, with controlled access (via access modifiers), protecting internal state from unintended interference.
- **Abstraction** – Complex implementation details are hidden, exposing only what's necessary, which simplifies usage.
- **Polymorphism** – The same interface can represent different underlying forms, enabling flexible and extensible code (e.g., one method call behaving differently based on the object type).
- **Maintainability & Scalability** – Changes in one module have minimal ripple effects, making large codebases easier to scale and refactor.

**Good closing line for the interview:** "OOP isn't just a syntax feature — it's a design philosophy that maps real-world entities into software, making systems easier to extend and maintain over time."

---

## 2. Difference between Abstract Class and Interface

| Aspect | Abstract Class | Interface |
|---|---|---|
| Methods | Can have both abstract and concrete (implemented) methods | Traditionally only abstract methods; since Java 8, can have `default` and `static` methods too |
| Variables | Can have instance variables (any access modifier) | Variables are implicitly `public static final` (constants) |
| Constructors | Can have constructors | Cannot have constructors |
| Inheritance | A class can extend only **one** abstract class | A class can implement **multiple** interfaces |
| Access Modifiers | Methods can be public, protected, private | Methods are implicitly public (until Java 9, which added private methods for internal use) |
| When to use | When classes share a common base with some shared implementation | When unrelated classes need to guarantee certain behavior (a "contract") |

**Key conceptual answer:** Use an **abstract class** when there's an "is-a" relationship with shared state/behavior. Use an **interface** when you want to define a capability/contract that unrelated classes can implement (like `Comparable` or `Runnable`).

---

## 3. Can an abstract class implement multiple interfaces and extend another abstract class simultaneously?

**Yes.** An abstract class can:
- Extend **one** other abstract (or concrete) class — Java supports single inheritance for classes.
- Implement **multiple** interfaces — there's no limit here.

```java
abstract class Vehicle {
    abstract void start();
}

interface Insurable {
    void insure();
}

interface Taxable {
    void payTax();
}

abstract class Car extends Vehicle implements Insurable, Taxable {
    // Car can choose to implement some methods and leave others abstract
    public void insure() {
        System.out.println("Car insured");
    }
    // start() and payTax() remain abstract — fine, since Car is abstract
}
```

The abstract class isn't required to implement all the abstract/interface methods immediately — it can leave some unimplemented since it's abstract itself. The **first concrete subclass** must implement all remaining abstract methods.

---

## 4. Difference between JDK, JVM, and JRE

- **JVM (Java Virtual Machine)** – The runtime engine that actually executes Java bytecode. It's platform-specific (different JVM implementations for Windows/Linux/Mac) but makes Java "write once, run anywhere" possible. It handles memory management, garbage collection, and bytecode interpretation/JIT compilation.

- **JRE (Java Runtime Environment)** – JVM + core libraries (like `java.lang`, `java.util`, etc.) + supporting files needed to **run** Java applications. It does NOT include development tools like a compiler.

- **JDK (Java Development Kit)** – JRE + development tools like `javac` (compiler), debugger, `javadoc`, etc. This is what you need to **write and compile** Java code.

**Simple analogy:** JVM is the engine, JRE is the engine + everything needed to drive the car (run programs), and JDK is the full toolkit — engine + driving capability + tools to build the car itself (develop programs).

**Relationship:** JDK ⊃ JRE ⊃ JVM

---

## 5. Can we install JRE separately without JDK? If yes, how does it impact development and execution?

**Yes**, historically JRE was distributed as a standalone package separate from JDK (pre-Java 11). 

**Impact:**
- **Execution is fine** — You can run any compiled `.class` file or `.jar` using just the JRE, since it has the JVM and standard libraries.
- **Development is NOT possible** — You cannot compile `.java` source files because JRE lacks `javac` and other dev tools. So writing, compiling, and debugging Java code requires the JDK.

**Important current-state note:** Since **Java 11**, Oracle stopped offering JRE as a separate standalone downloadable package. Now, JDK is the primary distribution, and you typically either install the full JDK or use `jlink` to create a custom minimal runtime image containing only the modules you need. So in modern Java, the JDK/JRE separation as a "what you download" choice is largely gone — though the *conceptual* distinction (compile-time tools vs. runtime-only) still exists.

---

## 6. If a static block throws an exception, what happens to the class loading process?

This is a sharper question — good that you're prepping it.

When a **static initializer block** throws an exception:

1. The JVM wraps it in an **`ExceptionInInitializerError`** (which extends `Error`, not `Exception`).
2. **Class initialization fails permanently.** The class is marked as "erroneous" and will **never be successfully initialized** in that JVM instance.
3. Any subsequent attempt to use that class — even in a `try-catch` — will throw a **`NoClassDefFoundError`**, not the original exception.

```java
class Demo {
    static int x = 10 / 0; // ArithmeticException in static init
}

public class Test {
    public static void main(String[] args) {
        try {
            Demo d = new Demo();
        } catch (Throwable t) {
            System.out.println(t); // java.lang.ExceptionInInitializerError
        }
        
        try {
            Demo d2 = new Demo(); // Second attempt
        } catch (Throwable t) {
            System.out.println(t); // java.lang.NoClassDefFoundError
        }
    }
}
```

**Why this matters:** Static blocks run once, when the class is loaded — there's no "retry." This is why it's risky to put anything that can fail (I/O, parsing, network calls) inside a static block without careful exception handling *within* the block itself.

---

## 7. How do you handle exceptions in Java?

Structure your answer around the mechanism, then best practices:

**Mechanism:**
```java
try {
    // risky code
} catch (SpecificException e) {
    // handle specific exception
} catch (Exception e) {
    // handle general exception
} finally {
    // cleanup code, always runs
}
```

**Best practices to mention (this is what separates a good answer from a textbook one):**
- Catch **specific exceptions** before general ones (compiler enforces this order anyway).
- Avoid swallowing exceptions silently (empty catch blocks) — at minimum log them.
- Use **try-with-resources** for closing resources (Streams, Connections) automatically:
  ```java
  try (FileReader fr = new FileReader("file.txt")) {
      // use fr
  } catch (IOException e) {
      e.printStackTrace();
  }
  ```
- Create **custom exceptions** for domain-specific error handling when needed.
- Don't use exceptions for normal control flow (performance and readability cost).
- Re-throw with context (`throw new ServiceException("Failed to process order", e)`) rather than losing the original stack trace.

---

## 8. What are the different types of exceptions in Java?

Two main categories, both under `Throwable`:

**1. Checked Exceptions**
- Checked at **compile-time**; must be either caught or declared with `throws`.
- Extend `Exception` (but not `RuntimeException`).
- Examples: `IOException`, `SQLException`, `ClassNotFoundException`.
- Represent conditions a well-written application should anticipate and recover from.

**2. Unchecked Exceptions (Runtime Exceptions)**
- Not checked at compile-time; occur during execution.
- Extend `RuntimeException`.
- Examples: `NullPointerException`, `ArrayIndexOutOfBoundsException`, `ArithmeticException`, `ClassCastException`.
- Usually represent programming bugs.

**3. Errors**
- Extend `Error`, not meant to be caught/handled by application code.
- Examples: `OutOfMemoryError`, `StackOverflowError`.
- Represent serious problems usually outside the application's control (JVM-level issues).

```
Throwable
├── Exception
│   ├── RuntimeException (unchecked)
│   └── (other checked exceptions like IOException)
└── Error (unchecked, not meant to be handled)
```

---

## 9. Do you know about the finally block?

Yes — `finally` is a block that **always executes** after a `try` block, regardless of whether an exception was thrown or caught, **except in a few JVM-level edge cases** (covered in Q10).

**Purpose:** Used for cleanup code — closing files, releasing database connections, closing network sockets — anything that *must* run regardless of success or failure.

```java
try {
    connection = dataSource.getConnection();
    // do work
} catch (SQLException e) {
    log.error("DB error", e);
} finally {
    if (connection != null) {
        connection.close(); // always runs, ensures no resource leak
    }
}
```

**Note worth mentioning:** Since Java 7, **try-with-resources** has largely replaced manual `finally`-based cleanup for `Closeable`/`AutoCloseable` resources, since it's less error-prone.

---

## 10. Are there situations where finally may NOT execute?

Yes — this is a good "I know the edge cases" answer. `finally` will **not** execute in these scenarios:

1. **JVM shutdown via `System.exit()`** called inside the `try` or `catch` block:
   ```java
   try {
       System.exit(0); // JVM terminates immediately
   } finally {
       System.out.println("This will NOT print");
   }
   ```

2. **JVM crashes** — e.g., a fatal error like `OutOfMemoryError` that brings down the JVM, or a native crash.

3. **Infinite loop or blocking call** in the `try` or `catch` block that never completes — `finally` is technically "pending" forever since control never leaves the block.

4. **Daemon thread killed abruptly** — if the thread executing the try-finally is a daemon thread and the JVM exits because all non-daemon threads have finished, the `finally` may not get a chance to run.

5. **Power failure / OS-level kill (`kill -9`)** — outside the JVM's control entirely.

**Good way to phrase your closing point:** "In normal application flow — including when exceptions are thrown, caught, or even when there's a `return` statement in the `try` block — `finally` is guaranteed to run. The exceptions are really at the level of abrupt JVM termination, not normal Java program control flow."

---

Continuing on — these move into Java 8 and Collections, both heavily tested areas for Java Developer roles.

## 11. Features of Java 8

Java 8 was a landmark release. Key features to mention:

- **Lambda Expressions** – Enable functional-style programming; treat functionality as a method argument.
- **Functional Interfaces** – Interfaces with a single abstract method (SAM), annotated with `@FunctionalInterface`, that lambdas can implement.
- **Stream API** – For processing collections in a declarative, functional style (filter, map, reduce, etc.) with support for parallel processing.
- **Default and Static Methods in Interfaces** – Allow interfaces to have method bodies, enabling backward-compatible API evolution.
- **Optional class** – Helps avoid `NullPointerException` by explicitly representing the presence/absence of a value.
- **New Date and Time API (`java.time`)** – Replaced the flawed old `Date`/`Calendar` classes with immutable, thread-safe classes like `LocalDate`, `LocalTime`, `LocalDateTime`.
- **Method References** – Shorthand for lambdas that just call an existing method, e.g., `String::toUpperCase`.
- **Nashorn JavaScript Engine** – Allowed running JS code on the JVM (later deprecated/removed in Java 15).
- **Collectors / Stream collecting utilities** – `Collectors.toList()`, `groupingBy()`, etc.

**Good framing line:** "Java 8 essentially brought functional programming concepts into Java, making code more concise and expressive while staying backward compatible."

---

## 12. What are Functional Interfaces in Java?

A **functional interface** is an interface that has **exactly one abstract method**, though it can have any number of default or static methods.

```java
@FunctionalInterface
interface Calculator {
    int operate(int a, int b); // single abstract method
    
    default void printInfo() { // allowed
        System.out.println("Calculator interface");
    }
}
```

- The `@FunctionalInterface` annotation is **optional but recommended** — it tells the compiler to enforce the single-abstract-method rule, catching accidental additions at compile time.
- Functional interfaces are the foundation that makes **lambda expressions** possible — a lambda is essentially a concise implementation of a functional interface's abstract method.
- Java provides many built-in functional interfaces in `java.util.function`: `Function`, `Predicate`, `Consumer`, `Supplier`, `Runnable`, `Callable`, `Comparator`, etc.

```java
Calculator add = (a, b) -> a + b;
System.out.println(add.operate(3, 4)); // 7
```

---

## 13. Designing a custom operation passed to multiple utility methods — how does a functional interface help, and how would you use a lambda?

This is a "design thinking" question — they want to see if you can apply the concept practically, not just define it.

**The problem:** You have multiple utility methods that need to perform a custom operation on data, but you don't want to write a separate method (or class) for every variation of that operation.

**The solution:** Define (or reuse) a functional interface representing the "shape" of the operation, then pass different lambda implementations to the utility method depending on the behavior you need.

```java
@FunctionalInterface
interface Operation {
    int apply(int a, int b);
}

class MathUtils {
    public static int compute(int a, int b, Operation op) {
        return op.apply(a, b);
    }
}

public class Test {
    public static void main(String[] args) {
        int sum = MathUtils.compute(5, 3, (a, b) -> a + b);
        int product = MathUtils.compute(5, 3, (a, b) -> a * b);
        int max = MathUtils.compute(5, 3, Math::max); // method reference works too

        System.out.println(sum + " " + product + " " + max); // 8 15 5
    }
}
```

**Why this is powerful (mention this for a strong answer):**
- The utility method (`compute`) stays generic and doesn't need to change every time you need a new operation.
- You avoid creating multiple small classes implementing an interface (the old anonymous-class boilerplate).
- It promotes **composition over duplication** — behavior is injected, not hardcoded.

If the operation is generic enough, you'd often just reuse a built-in interface like `BinaryOperator<Integer>` instead of writing your own.

---

## 14. Roles of Predicate, Function, and Consumer in Java

All three live in `java.util.function` and represent different "shapes" of operations:

| Interface | Method | Signature | Purpose |
|---|---|---|---|
| `Predicate<T>` | `test(T t)` | `T → boolean` | Tests a condition, returns true/false. Used for filtering. |
| `Function<T, R>` | `apply(T t)` | `T → R` | Transforms input of type T into output of type R. Used for mapping/transformation. |
| `Consumer<T>` | `accept(T t)` | `T → void` | Takes an input and performs an action, returns nothing. Used for side effects (printing, saving, logging). |

**Code example tying all three together (great for showing practical understanding):**

```java
List<String> names = List.of("Alice", "Bob", "Charlie", "Dave");

Predicate<String> startsWithA = name -> name.startsWith("A");
Function<String, Integer> getLength = String::length;
Consumer<String> printer = System.out::println;

names.stream()
     .filter(startsWithA)      // Predicate: keep names starting with 'A'
     .map(getLength)           // Function: convert name -> length
     .forEach(System.out::println); // Consumer-like behavior in forEach
```

**Bonus interfaces worth mentioning if asked to expand:**
- `Supplier<T>` – takes no input, returns a value (`() → T`). Used for lazy generation, like factory methods.
- `BiFunction<T, U, R>` – two inputs, one output.
- `UnaryOperator<T>` / `BinaryOperator<T>` – special cases of `Function`/`BiFunction` where input and output types are the same.

---

## 15. Do you know collections in Java?

This is an open invitation to demonstrate breadth. Structure your answer around the **Collection hierarchy**:

**Core interfaces:**
- **`Collection`** – root interface
  - **`List`** – ordered, allows duplicates (`ArrayList`, `LinkedList`, `Vector`)
  - **`Set`** – no duplicates (`HashSet`, `LinkedHashSet`, `TreeSet`)
  - **`Queue`** – FIFO-ish processing (`PriorityQueue`, `ArrayDeque`)
- **`Map`** – key-value pairs, *not* part of `Collection` interface hierarchy but still part of the Collections Framework (`HashMap`, `TreeMap`, `LinkedHashMap`, `ConcurrentHashMap`)

**Key supporting concepts to mention:**
- The `Collections` utility class (static methods like `sort()`, `reverse()`, `synchronizedList()`).
- Iteration via `Iterator`, enhanced for-loop, or Streams.
- Thread-safety considerations — most standard collections (`ArrayList`, `HashMap`) are **not** thread-safe; need `Collections.synchronizedXxx()` or concurrent variants (`java.util.concurrent` package) for multi-threaded use.

**Strong closing line:** "Choosing the right collection is really about understanding the trade-offs — ordering guarantees, duplicate handling, thread-safety, and performance characteristics for insertion/lookup/deletion."

---

## 16. Difference between HashMap and ConcurrentHashMap

| Aspect | HashMap | ConcurrentHashMap |
|---|---|---|
| Thread-safety | **Not thread-safe** — concurrent modification can cause data corruption or infinite loops (in older Java versions) | **Thread-safe**, designed for concurrent access |
| Locking mechanism | None | Uses fine-grained locking — in Java 8+, it uses CAS (Compare-And-Swap) operations and synchronized blocks **per bucket/node**, not the whole map |
| Null keys/values | Allows **one null key** and multiple null values | Does **not** allow null keys or null values (throws `NullPointerException`) — this is intentional, to avoid ambiguity in concurrent reads |
| Performance | Faster in single-threaded context (no locking overhead) | Slightly more overhead, but scales much better under concurrent access |
| Iteration | Fail-fast iterator — throws `ConcurrentModificationException` if modified during iteration | Fail-safe (weakly consistent) iterator — doesn't throw exception, may not reflect very recent updates during iteration |
| Use case | Single-threaded or externally synchronized contexts | Multi-threaded environments needing concurrent reads/writes without external locking |

**Worth mentioning for depth:** Older approach was `Collections.synchronizedMap(new HashMap<>())`, which locks the **entire map** for every operation — much worse for concurrency than `ConcurrentHashMap`'s segmented/bucket-level locking.

---

## 17. Difference between ArrayList and LinkedList

| Aspect | ArrayList | LinkedList |
|---|---|---|
| Underlying structure | Dynamic (resizable) array | Doubly linked list |
| Access (get by index) | **O(1)** — direct index access | **O(n)** — must traverse from head/tail |
| Insertion/Deletion (middle) | **O(n)** — requires shifting elements | **O(1)** once you have a reference to the node, but **O(n)** to find that position |
| Insertion/Deletion (at ends) | O(1) amortized at the end, O(n) at the beginning | O(1) at both ends (head and tail) |
| Memory overhead | Lower — just the array and some unused capacity | Higher — each node stores data + two pointers (prev/next) |
| Implements | `List`, `RandomAccess` | `List`, `Deque` (so it can be used as a stack/queue too) |

---

## 18. When should you choose ArrayList over LinkedList, and vice versa?

**Choose `ArrayList` when:**
- You need **frequent random access** by index (`get(i)`) — it's O(1) vs O(n) for LinkedList.
- Insertions/deletions mostly happen **at the end** of the list.
- Memory efficiency matters (no extra pointer overhead per element).
- This covers the vast majority of real-world use cases — which is why **`ArrayList` is the default/recommended choice** in most situations.

**Choose `LinkedList` when:**
- You have **frequent insertions/deletions at the beginning or middle** of the list, and you already have a reference/iterator at that position (otherwise you still pay O(n) to find it).
- You need **Deque/Queue behavior** — `LinkedList` implements `Deque`, so it's good for implementing stacks, queues, or when frequently adding/removing from both ends.

**Honest practical note (this shows real-world maturity, good for interviews):** In modern practice, `LinkedList` is rarely the right choice — even for queue-like behavior, `ArrayDeque` usually outperforms `LinkedList` due to better cache locality (contiguous memory vs scattered nodes). Many experienced developers default to `ArrayList` almost always, and reach for `LinkedList` only in fairly specific scenarios.

---

Now we're into Multithreading — one of the most heavily probed areas for Java Developer interviews, especially at mid-to-senior level. Let's go through these carefully.

## 26. What is Threading in Java?

A **thread** is the smallest unit of execution within a process. **Multithreading** is the ability to run multiple threads concurrently within a single program, sharing the same memory space (heap), while each thread has its own stack and program counter.

**Why it matters (good to mention):**
- Enables **concurrent execution** — better CPU utilization, especially on multi-core systems.
- Useful for tasks like handling multiple client requests (web servers), background processing (file I/O while UI stays responsive), or parallelizing computation.
- Java has built-in support for threading via the `Thread` class, `Runnable` interface, and the higher-level `java.util.concurrent` package (Executors, Futures, etc.).

**Key distinction to mention:** Multithreading ≠ Multiprocessing. Threads within a process share memory (heap, static data), which makes communication faster but introduces risks like race conditions — unlike processes, which have isolated memory.

---

## 27. Different ways to create a thread in Java

**1. Extending the `Thread` class**
```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Running via Thread subclass");
    }
}
```

**2. Implementing the `Runnable` interface**
```java
class MyRunnable implements Runnable {
    public void run() {
        System.out.println("Running via Runnable");
    }
}
```

**3. Implementing `Callable` (returns a result, can throw checked exceptions)** — used with `ExecutorService`
```java
class MyCallable implements Callable<Integer> {
    public Integer call() {
        return 42;
    }
}
```

**4. Using Lambda expressions (since Runnable is a functional interface)**
```java
Thread t = new Thread(() -> System.out.println("Running via lambda"));
```

**5. Using `ExecutorService` / Thread Pools** (modern, preferred approach for production code)
```java
ExecutorService executor = Executors.newFixedThreadPool(2);
executor.submit(() -> System.out.println("Running via ExecutorService"));
executor.shutdown();
```

**Good closing line:** "While extending `Thread` and implementing `Runnable` are the textbook answers, in real production code, you'd almost always use `ExecutorService` or higher-level concurrency utilities rather than managing raw threads manually."

---

## 28. Creating a thread by extending Thread class and starting it

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread is running: " + Thread.currentThread().getName());
    }
}

public class Test {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start();   // starts a new thread, run() executes asynchronously
        
        // NOTE: calling t1.run() directly would just run it synchronously
        // on the main thread, NOT create a new thread!
    }
}
```

**Key point to emphasize (commonly asked as a follow-up):** Always call `start()`, never `run()` directly, if you want actual concurrent execution. `start()` creates a new call stack and triggers the JVM/OS to schedule a new thread, which then internally calls `run()`. Calling `run()` directly just executes it like a normal method call on the current thread.

---

## 29. When to use Runnable vs Thread

**Prefer `Runnable` over extending `Thread`** in almost all cases. Reasons:

1. **Java doesn't support multiple inheritance of classes.** If your class extends `Thread`, it can't extend anything else. Implementing `Runnable` keeps that door open.
2. **Separation of concerns** — `Runnable` represents *a task*, while `Thread` represents *the mechanism executing it*. This is cleaner design (favor composition over inheritance).
3. **Reusability** — the same `Runnable` instance can be passed to multiple threads, an `ExecutorService`, etc. A `Thread` subclass is tied to thread-specific mechanics.
4. **Works well with the modern concurrency API** — `ExecutorService`, thread pools, and `Callable` are all designed around the task (`Runnable`/`Callable`) and execution mechanism (`Executor`) being decoupled.

**When would you actually extend `Thread`?** Rarely — maybe if you need to override other `Thread` methods beyond `run()`, but even that's uncommon in practice.

**Good line for interviews:** "`Runnable` defines *what* to run, `Thread` defines *how* to run it — keeping them separate is better design."

---

## 30. Different states of a Thread Life Cycle

Java thread states are defined in the `Thread.State` enum:

1. **NEW** – Thread object created, but `start()` not yet called.
2. **RUNNABLE** – After `start()` is called; thread is either running or ready/waiting for CPU time (Java doesn't distinguish "ready" vs "running" as separate states).
3. **BLOCKED** – Thread is waiting to acquire a monitor lock (e.g., trying to enter a `synchronized` block held by another thread).
4. **WAITING** – Thread is waiting indefinitely for another thread's signal (via `wait()`, `join()` with no timeout, or `LockSupport.park()`).
5. **TIMED_WAITING** – Like WAITING, but with a timeout (`sleep(ms)`, `wait(ms)`, `join(ms)`).
6. **TERMINATED** – Thread has completed execution (`run()` finished, or it exited due to an exception).

```
NEW → RUNNABLE → (BLOCKED / WAITING / TIMED_WAITING) → RUNNABLE → TERMINATED
```

---

## 31 & 32. Can a thread be restarted after completing? Why/why not?

**No, a thread cannot be restarted once it has completed execution (reached TERMINATED state).**

**Why:**
- Once a thread finishes its `run()` method (or throws an uncaught exception), it transitions to the **TERMINATED** state, which is a **terminal state** — there's no transition path back to NEW or RUNNABLE.
- A `Thread` object internally tracks its lifecycle state. Once terminated, calling `start()` again is explicitly disallowed by the JVM/Thread implementation, because the underlying OS thread resources have already been released and the JVM bookkeeping (call stack, thread-specific state) is gone.
- Conceptually, a `Thread` object is a **one-time-use wrapper** around an actual OS-level thread of execution — it's not designed to be "reset."

**Practical implication to mention:** If you need to "rerun" the same task multiple times, you should create a new `Thread` instance each time (with the same `Runnable`), or better — use an `ExecutorService`, which is designed precisely for reusing worker threads to execute multiple tasks over time.

---

## 33. What happens if you try to start an already-started thread?

It throws **`IllegalThreadStateException`** (a subclass of `RuntimeException`), at runtime.

```java
Thread t = new Thread(() -> System.out.println("Hello"));
t.start();
t.start(); // throws java.lang.IllegalThreadStateException
```

This applies whether the thread is still running, or has already completed — `start()` can only be called **once** on any given `Thread` instance, full stop.

---

## 34. What is a deadlock?

A **deadlock** occurs when two or more threads are blocked forever, each waiting for a resource (lock) held by another thread in the group — creating a circular wait with no possible resolution.

**Classic example:**
```java
Object lockA = new Object();
Object lockB = new Object();

// Thread 1
synchronized (lockA) {
    Thread.sleep(100);
    synchronized (lockB) { ... }  // waits for lockB, held by Thread 2
}

// Thread 2
synchronized (lockB) {
    Thread.sleep(100);
    synchronized (lockA) { ... }  // waits for lockA, held by Thread 1
}
```

Thread 1 holds `lockA` and wants `lockB`. Thread 2 holds `lockB` and wants `lockA`. Neither can proceed — deadlock.

**The four necessary conditions for deadlock (good to mention if asked to go deeper):**
1. Mutual exclusion (resources can't be shared)
2. Hold and wait (a thread holds one resource while waiting for another)
3. No preemption (a resource can't be forcibly taken away)
4. Circular wait (a cycle of threads each waiting on the next)

---

## 35. How can you avoid a deadlock?

1. **Lock ordering** – Always acquire multiple locks in a **consistent, predefined order** across all threads. If every thread locks `lockA` before `lockB`, the circular wait condition is broken.
2. **Lock timeout** – Use `tryLock(timeout)` (from `java.util.concurrent.locks.Lock`) instead of indefinite blocking `synchronized`, so a thread gives up and retries/backs off if it can't acquire a lock in time.
3. **Avoid nested locks** – Minimize situations where a thread needs to hold multiple locks simultaneously.
4. **Use higher-level concurrency utilities** – `java.util.concurrent` classes (`ConcurrentHashMap`, `BlockingQueue`, `ExecutorService`, atomic variables) are designed to avoid manual lock management altogether.
5. **Deadlock detection tools** – Use thread dumps (`jstack`) or profilers to detect deadlocks in production/staging if they're suspected.

**Good summary line:** "The most reliable strategy is consistent lock ordering — most deadlocks in real systems come from inconsistent locking sequences across different code paths."

---

## 36. Do you know wait() and notify()?

Yes — these are methods on `Object` (not `Thread`) used for **inter-thread communication**, typically within a `synchronized` block.

- **`wait()`** – Causes the current thread to release the lock and pause execution until another thread calls `notify()` or `notifyAll()` on the same object.
- **`notify()`** – Wakes up **one** waiting thread (arbitrary choice if multiple are waiting).
- **`notifyAll()`** – Wakes up **all** waiting threads; they then compete to reacquire the lock.

```java
class SharedResource {
    private boolean available = false;

    synchronized void produce() {
        available = true;
        notify(); // wake up a waiting consumer
    }

    synchronized void consume() throws InterruptedException {
        while (!available) {
            wait(); // releases lock, waits to be notified
        }
        available = false;
    }
}
```

**Important details to mention (shows depth):**
- Must be called **within a `synchronized` block**, otherwise throws `IllegalMonitorStateException`.
- `wait()` should always be called in a **loop**, checking the condition (`while`, not `if`) — to guard against **spurious wakeups**.
- Modern alternative: `java.util.concurrent` provides higher-level constructs like `BlockingQueue`, `CountDownLatch`, `Semaphore`, and `Condition` (from `Lock`), which are generally preferred over raw `wait`/`notify` in production code.

---

## 37. Writing to a file from multiple threads — synchronization techniques

This is a practical/design question. A few valid approaches, mention trade-offs:

**1. `synchronized` block/method around the write operation**
```java
synchronized void writeToFile(String data) {
    // write data
}
```
Simple but can become a bottleneck under high contention.

**2. `ReentrantLock`** – more flexible than `synchronized` (supports `tryLock`, fairness policies, interruptible locking).

**3. Single-threaded writer via `ExecutorService` (Producer-Consumer pattern)** – Often the **best practical approach**: have multiple threads submit write requests to a **`BlockingQueue`**, and a **single dedicated writer thread** consumes from the queue and writes sequentially. This avoids lock contention entirely on the file resource itself.
```java
BlockingQueue<String> queue = new LinkedBlockingQueue<>();
// Producer threads: queue.put(data);
// Single consumer thread: while(true) { write(queue.take()); }
```

**4. `FileChannel` with `FileLock`** – For OS-level file locking when coordinating across processes, not just threads.

**5. AtomicWriter pattern / `java.nio.file.Files.write` with append + external synchronization** – simpler scenarios.

**Best practical answer for an interview:** "I'd lean towards the producer-consumer pattern with a `BlockingQueue` and single writer thread — it avoids contention, guarantees write ordering, and decouples the producing threads from I/O latency."

---

## 38. What is a race condition?

A **race condition** occurs when **multiple threads access and modify shared data concurrently**, and the final outcome depends on the unpredictable timing/order of thread execution — leading to incorrect or inconsistent results.

**Classic example:**
```java
class Counter {
    private int count = 0;
    
    void increment() {
        count++; // NOT atomic! Read-modify-write — 3 separate steps
    }
}
```
If two threads call `increment()` simultaneously, both might read `count = 5` before either writes back `6`, so the final value becomes `6` instead of the expected `7` — one increment is lost.

---

## 39. How can a race condition be prevented?

1. **Synchronization (`synchronized` keyword)** – Ensures only one thread executes the critical section at a time.
   ```java
   synchronized void increment() { count++; }
   ```
2. **Locks (`ReentrantLock`)** – More control than `synchronized` (explicit lock/unlock, tryLock, fairness).
3. **Atomic variables (`AtomicInteger`, `AtomicLong`, etc.)** – Use CAS (Compare-And-Swap) operations under the hood for lock-free thread safety on simple variables.
   ```java
   AtomicInteger count = new AtomicInteger(0);
   count.incrementAndGet(); // atomic, no race condition
   ```
4. **Immutable objects** – If state can't change after creation, there's no race condition to worry about — a very effective design-level prevention strategy.
5. **Thread-safe collections** – Use `ConcurrentHashMap`, `CopyOnWriteArrayList`, etc., instead of manually synchronizing standard collections.
6. **Minimize shared mutable state** – The best prevention is architectural: design so threads don't need to share mutable state in the first place (e.g., thread-local variables, message-passing designs).

**Strong closing line:** "The general philosophy is: prefer immutability and atomic operations over manual locking wherever possible — locks are correct but easy to misuse, while atomics and immutable design eliminate the race condition by construction."

---
These move into JVM internals and design patterns — important for showing you can think beyond syntax into system behavior and design. Let's go through them.

## 40. How does Garbage Collection work in Java?

**Core idea:** Java manages memory automatically — the **Garbage Collector (GC)** identifies and reclaims memory occupied by objects that are no longer reachable from any live reference (GC roots: local variables, active threads, static fields, etc.).

**Heap structure (important to mention):**
- **Young Generation** – split into **Eden** space and two **Survivor** spaces (S0, S1). New objects are allocated here.
- **Old Generation (Tenured)** – objects that survive multiple GC cycles in the young generation get **promoted** here.
- **Metaspace** (replaced PermGen since Java 8) – stores class metadata, outside the heap.

**GC process (generational hypothesis: most objects die young):**
1. **Minor GC** – Cleans the Young Generation. When Eden fills up, a GC cycle runs; live objects are copied to a Survivor space, and dead objects are reclaimed. Objects that survive enough cycles (controlled by an age threshold) get promoted to Old Gen.
2. **Major/Full GC** – Cleans the Old Generation (and sometimes the whole heap). This is more expensive and causes longer pause times.

**Common GC algorithms (mention a couple to show breadth):**
- **Serial GC** – Single-threaded, simple, good for small applications.
- **Parallel GC** – Multi-threaded, throughput-focused (default in older JVMs).
- **CMS (Concurrent Mark Sweep)** – Deprecated/removed in newer Java versions; aimed at low pause times.
- **G1 (Garbage First)** – Default since Java 9; divides heap into regions, balances throughput and pause time, good for large heaps.
- **ZGC / Shenandoah** – Modern low-latency collectors (sub-millisecond pauses), suited for very large heaps.

**Key mechanism:** Most JVM GCs use a **mark-and-sweep** approach (mark reachable objects, sweep/reclaim the rest), often combined with **compaction** to reduce fragmentation, and **copying** collectors for the young generation.

**Good closing line:** "GC's core promise is automatic memory management, but understanding the generational model and collector trade-offs matters a lot when you're tuning for latency vs throughput in production."

---

## 41. Analyzing GC logs and tuning JVM for production GC performance

This is a practical, experience-based question — structure your answer as a process.

**Step 1: Enable and collect GC logs**
```bash
-Xlog:gc*:file=gc.log:time,uptime,level,tags
```
(Java 9+ unified logging; older versions used `-XX:+PrintGCDetails -XX:+PrintGCDateStamps`)

**Step 2: Analyze with tooling**
- Use tools like **GCViewer**, **GCEasy**, or **Eclipse MAT** (for heap dumps) to visualize pause times, frequency, and heap usage trends.
- Look for: frequency of Minor vs Major GC, pause duration trends, heap occupancy after GC (if it stays high, may indicate a memory leak), and promotion rate into Old Gen.

**Step 3: Identify the problem pattern**
- **Frequent Minor GCs** → Young generation might be too small; objects are being promoted too quickly.
- **Long Full GC pauses** → Old generation pressure, possibly due to memory leaks or undersized heap.
- **High promotion rate** → Objects living longer than expected; could indicate inefficient caching or premature tenuring.

**Step 4: Tune based on findings**
- Adjust heap size: `-Xms`, `-Xmx` (set them **equal** in production to avoid resize pauses).
- Adjust Young Gen size: `-Xmn` or ratio-based `-XX:NewRatio`.
- Switch GC algorithm if needed: e.g., move to **G1GC** (`-XX:+UseG1GC`) for balanced latency/throughput, or **ZGC** for ultra-low-latency requirements with large heaps.
- Tune G1-specific pause targets: `-XX:MaxGCPauseMillis=200`.

**Step 5: Validate with load testing** before rolling changes into production — GC tuning is iterative, not a one-shot fix.

**Strong line to use:** "I'd treat GC tuning as a feedback loop — collect logs, identify the bottleneck pattern (frequency vs pause duration vs promotion rate), make one targeted change at a time, and re-measure. Changing multiple JVM flags simultaneously makes it hard to know what actually helped."

---

## 42. What are transient fields in Java?

The **`transient`** keyword marks a field so that it is **excluded from the default serialization process**. When an object is serialized (converted to a byte stream via `ObjectOutputStream`), transient fields are skipped — only **non-transient** fields are written.

```java
class User implements Serializable {
    String username;
    transient String password; // won't be serialized
}
```

**Common use cases:**
- Sensitive data (passwords, security tokens) you don't want persisted/transmitted.
- Fields that are derived/computable from other fields, so storing them is redundant.
- Fields that hold non-serializable objects (e.g., a `Thread`, `Socket`, or a database connection) — these would cause a `NotSerializableException` if not marked transient.

---

## 43. What happens to a transient field on deserialization, and how do you restore it?

**What happens:** During deserialization, **transient fields are set to their default values** — `null` for objects, `0` for numeric primitives, `false` for booleans — since they were never written to the byte stream in the first place.

```java
class User implements Serializable {
    String username;
    transient String password;
}
// After deserialization: username = "original value", password = null
```

**How to restore the field — a few approaches:**

**1. Override `readObject()` to recompute/reset the value manually:**
```java
private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
    in.defaultReadObject(); // deserializes all non-transient fields normally
    this.password = fetchPasswordFromSecureStore(); // manually restore
}
```
This pairs naturally with a custom `writeObject()` if you need custom serialization logic too.

**2. Use `Externalizable` interface instead of `Serializable`** – gives you full manual control over both writing and reading every field, including ones that would otherwise be transient.

**3. Lazy initialization** – if the field is something like a cache or computed value, just recompute it lazily the first time it's accessed post-deserialization (e.g., via a getter that checks for null).

**Good line to mention:** "The key insight is that `readObject()` gives you a hook to add custom logic right after the default deserialization — that's the standard way to 'repair' transient state."

---

## 44. Design patterns — how comfortable are you?

This is open-ended; structure your answer by **category**, and mention a couple of examples + when you've used them (if you have real experience, mention it specifically — interviewers love concrete examples over textbook lists).

**Creational** (object creation mechanisms): Singleton, Factory, Abstract Factory, Builder, Prototype.

**Structural** (composing classes/objects): Adapter, Decorator, Facade, Proxy, Composite, Bridge.

**Behavioral** (object interaction/responsibility): Strategy, Observer, Command, Template Method, Chain of Responsibility, State.

**Good answer structure:** "I'm comfortable with the common creational and behavioral patterns — Singleton and Factory for object creation, Strategy and Observer for decoupling behavior, and Builder for constructing complex objects with many optional parameters. In practice, I've used [mention a real example if you have one, e.g., Strategy pattern for different payment processing methods, or Observer for event-driven notifications]."

---

## 45. SOLID Principles

Five principles for writing maintainable, extensible object-oriented code:

- **S — Single Responsibility Principle** – A class should have only one reason to change; it should do one thing well.
- **O — Open/Closed Principle** – Classes should be open for extension but closed for modification — add new behavior via new code (e.g., subclassing, composition), not by editing existing tested code.
- **L — Liskov Substitution Principle** – Subtypes must be substitutable for their base types without breaking the correctness of the program — if `B extends A`, you should be able to use `B` anywhere `A` is expected.
- **I — Interface Segregation Principle** – Clients shouldn't be forced to depend on methods they don't use; prefer several small, specific interfaces over one large general-purpose one.
- **D — Dependency Inversion Principle** – High-level modules shouldn't depend on low-level modules directly; both should depend on abstractions (interfaces). This is the principle behind **Dependency Injection**.

**Good closing line:** "SOLID isn't just theory — it's the reasoning behind why we use interfaces over concrete classes, why we favor dependency injection frameworks like Spring, and why we keep classes small and focused. It directly affects how testable and maintainable code is."

---

## 46. Difference between Factory and Abstract Factory

| Aspect | Factory Pattern | Abstract Factory Pattern |
|---|---|---|
| Purpose | Creates objects of **one type/family**, with the specific subtype decided at runtime | Creates **families of related objects** without specifying their concrete classes |
| Structure | Single factory method/class | A factory of factories — typically an interface with multiple factory methods, each creating a different related product |
| Complexity | Simpler, one level of abstraction | More complex, adds a layer for grouping related factories |
| Example use case | A `ShapeFactory` that returns `Circle`, `Square`, `Rectangle` based on input | A `UIFactory` that returns a whole family of related components (`Button`, `Checkbox`, `TextField`) consistent with a theme — e.g., `WindowsUIFactory` vs `MacUIFactory` |

**Code sketch to clarify the distinction:**

```java
// Factory Pattern - creates ONE product type
interface ShapeFactory {
    Shape createShape(String type);
}

// Abstract Factory - creates FAMILIES of related products
interface UIFactory {
    Button createButton();
    Checkbox createCheckbox();
}

class WindowsUIFactory implements UIFactory {
    public Button createButton() { return new WindowsButton(); }
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}

class MacUIFactory implements UIFactory {
    public Button createButton() { return new MacButton(); }
    public Checkbox createCheckbox() { return new MacCheckbox(); }
}
```

**One-line distinction to remember:** "Factory creates **one product**; Abstract Factory creates **a family of related products** that need to stay consistent with each other."

---

## 47. Singleton Design Pattern — code

The most interview-relevant version is **thread-safe lazy initialization using double-checked locking**, since it shows you understand both the pattern AND threading concerns together:

```java
public class Singleton {
    // volatile ensures visibility across threads and prevents instruction reordering
    private static volatile Singleton instance;

    // private constructor prevents instantiation from outside
    private Singleton() {}

    public static Singleton getInstance() {
        if (instance == null) {                    // first check (no locking, fast path)
            synchronized (Singleton.class) {
                if (instance == null) {             // second check (inside lock)
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

**Why double-checked locking (good to explain proactively):**
- The first `if` check avoids acquiring the lock on every call — once `instance` is set, subsequent calls skip synchronization entirely (good performance).
- The `synchronized` block ensures only one thread can create the instance.
- The second `if` check inside the lock prevents a second thread (which was waiting on the lock) from creating a duplicate instance.
- **`volatile` is essential** — without it, due to JVM instruction reordering, another thread could see a non-null but **partially constructed** object.

**Simpler, equally valid (often preferred) alternative — the Enum Singleton:**
```java
public enum Singleton {
    INSTANCE;
    
    public void doSomething() {
        System.out.println("Singleton method");
    }
}
```
This is **thread-safe by default**, handles serialization correctly out of the box, and protects against reflection-based attacks that can break traditional singleton implementations — Joshua Bloch (in *Effective Java*) recommends this as the best way to implement Singleton in Java.

**Good thing to mention if asked "which approach would you use?":** "For most production code, I'd actually reach for a Spring-managed singleton bean (`@Component`/`@Service`, default scope) rather than hand-rolling this pattern — the framework handles the lifecycle. But if I needed to implement it manually, I'd prefer the enum-based approach for its simplicity and built-in safety guarantees."

---
