# Java Interview Preparation (5 Years Experience)

Complete question-and-answer notes covering Core Java, Collections, Concurrency, JVM, Hibernate, Spring Boot, Kafka, Spring Security/JWT, Microservices, AWS and scenario-based questions.

## Table of Contents

1. [Batch 1: Core Java, Collections, Concurrency, Exceptions, Hibernate](#batch-1-core-java-collections-concurrency-exceptions-hibernate)
2. [Batch 2: HashMap, Executors, CompletableFuture, Transactions, GC, Heap](#batch-2-hashmap-executors-completablefuture-transactions-gc-heap)
3. [Batch 3: GC Control, Records, Interfaces, ExecutorService](#batch-3-gc-control-records-interfaces-executorservice)
4. [Batch 4: Quick Repeats](#batch-4-quick-repeats)
5. [Batch 5: Spring Boot](#batch-5-spring-boot)
6. [Batch 6: Kafka](#batch-6-kafka)
7. [Batch 7: Spring Security and JWT](#batch-7-spring-security-and-jwt)
8. [Batch 8: Microservices](#batch-8-microservices)
9. [Batch 9: AWS](#batch-9-aws)
10. [Batch 10: Scenario-Based Questions](#batch-10-scenario-based-questions)

---

# Batch 1: Core Java, Collections, Concurrency, Exceptions, Hibernate

## 1. How do you handle collisions in hashing?

A collision happens when two different keys map to the same bucket index.

**Main techniques:**
- **Separate chaining:** each bucket holds a linked list (or tree) of entries. This is what `HashMap` uses.
- **Open addressing:** if a slot is taken, probe for another one (linear probing, quadratic probing, double hashing). `ThreadLocalMap` uses linear probing.

**How HashMap does it:**
1. It computes `hash(key)` and finds the bucket index.
2. If the bucket is empty, it stores the entry.
3. If not, it walks the chain, using `equals()` to check whether the key already exists. If yes, it replaces the value. Otherwise it appends a new node.
4. **Java 8+:** when a bucket has 8 or more nodes and the table capacity is at least 64, the linked list converts to a red-black tree. Lookup in that bucket improves from O(n) to O(log n). It converts back to a list when the size drops to 6 or below.
5. When size exceeds `capacity × load factor (0.75)`, the table resizes (doubles) and entries are redistributed.

**Reducing collisions:** a good `hashCode()` that distributes evenly, immutable keys (like `String`, `Integer`), and a proper initial capacity.

**Likely follow-up:** "What's the `equals`/`hashCode` contract?" Equal objects must have equal hash codes, but equal hash codes don't imply equal objects.

---

## 2. Method hiding vs overriding

| | Overriding | Hiding |
|---|---|---|
| Applies to | Instance methods | Static methods (also fields) |
| Resolved | Runtime (dynamic binding) | Compile time (based on reference type) |
| Polymorphism | Yes | No |

```java
class Parent {
    static void show() { System.out.println("Parent static"); }
    void print()       { System.out.println("Parent instance"); }
}
class Child extends Parent {
    static void show() { System.out.println("Child static"); }   // hiding
    @Override
    void print()       { System.out.println("Child instance"); } // overriding
}

Parent p = new Child();
p.show();   // Parent static  (decided by reference type)
p.print();  // Child instance (decided by actual object)
```

**Overriding rules:**
- Same name and parameters, and the return type can be covariant (a subtype).
- Access modifier can't be more restrictive.
- Can't throw broader checked exceptions.
- `private`, `final` and `static` methods can't be overridden. A static method can't override an instance method or vice versa (compile error).
- Use `@Override` so the compiler catches mistakes.

**Extra points:**
- Fields are hidden too, never overridden: `parent.name` is resolved by reference type.
- A `final` static method can't be hidden either.
- `@Override` on a static method won't compile, which tells you it isn't an override.
- Hiding is a common source of confusing bugs, so most style guides say to avoid it.

---

## 3. `Arrays.asList()` vs `List.of()`

| | `Arrays.asList()` | `List.of()` (Java 9+) |
|---|---|---|
| Size | Fixed | Fixed |
| `set()` | Allowed | `UnsupportedOperationException` |
| `add()/remove()` | `UnsupportedOperationException` | `UnsupportedOperationException` |
| Nulls | Allowed | `NullPointerException` |
| Backed by array | Yes, changes write through | No, truly immutable |

```java
String[] arr = {"a", "b"};
List<String> l1 = Arrays.asList(arr);
l1.set(0, "x");          // works, and arr[0] becomes "x" too

List<String> l2 = List.of("a", "b");
l2.set(0, "x");          // UnsupportedOperationException
```

**Gotcha:** `Arrays.asList(new int[]{1,2,3})` returns `List<int[]>` with one element, because primitives aren't autoboxed as varargs. Also, `List.of(...).contains(null)` throws NPE.

For a mutable list, use `new ArrayList<>(Arrays.asList(...))`.

---

## 4. What is a deadlock?

A deadlock is when two or more threads are blocked forever, each waiting for a lock held by another.

**Four necessary (Coffman) conditions:**
1. **Mutual exclusion:** a resource is held by one thread at a time.
2. **Hold and wait:** a thread holds one resource while waiting for another.
3. **No preemption:** locks can't be forcibly taken away.
4. **Circular wait:** T1 waits for T2, T2 waits for T1.

```java
// Thread 1: lock(A) -> lock(B)
// Thread 2: lock(B) -> lock(A)
```

**Prevention:**
- Always acquire locks in a consistent global order.
- Use `tryLock(timeout)` instead of blocking forever.
- Keep synchronized blocks small and avoid nested locks.
- Prefer higher-level utilities (`ConcurrentHashMap`, `Atomic*`).

**Detection:** thread dump (`jstack <pid>`), JConsole/VisualVM, or `ThreadMXBean.findDeadlockedThreads()`.

**Related:** livelock (threads keep reacting to each other but make no progress) and starvation (a thread never gets CPU or lock access).

---

## 5. When does a Singleton break (create more than one instance)?

| Scenario | Fix |
|---|---|
| Non-thread-safe lazy init (race condition) | Synchronized method, double-checked locking with `volatile`, or Bill Pugh holder class |
| **Reflection** calls the private constructor via `setAccessible(true)` | Throw an exception in the constructor if the instance exists, or use an enum |
| **Serialization/deserialization** creates a new object | Implement `readResolve()` returning the instance |
| **Cloning** if the class implements `Cloneable` | Override `clone()` to throw an exception or return the same instance |
| **Multiple ClassLoaders** (app servers, OSGi) each load the class separately | Share the class via a parent classloader |
| **Multiple JVMs / clustered deployment** | Singleton is per-JVM only, so use a distributed lock or external store |
| Constructor throws in static initializer | Class fails with `ExceptionInInitializerError`, later `NoClassDefFoundError`, so the instance is never available |

**Best practice:** enum singleton. It's thread-safe, serialization-safe and reflection-safe.

```java
public enum AppConfig { INSTANCE; }
```

**Spring note:** a Spring singleton bean is one instance per application context, not per JVM.

---

## 6. What is connection pooling and how do you achieve it?

Opening a DB connection is expensive (TCP handshake, authentication, session setup). A **connection pool** creates a set of connections up front and reuses them. Threads borrow a connection, use it, and return it.

**How it works:**
1. The pool initializes with N connections.
2. `getConnection()` hands out an idle one, or waits (up to a timeout) if none are free.
3. `connection.close()` doesn't really close it. A proxy returns it to the pool.
4. The pool validates connections and evicts stale ones.

**In practice (Spring Boot uses HikariCP by default):**
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/db
spring.datasource.hikari.maximum-pool-size=10
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.connection-timeout=30000
spring.datasource.hikari.idle-timeout=600000
spring.datasource.hikari.max-lifetime=1800000
spring.datasource.hikari.leak-detection-threshold=20000
```

Other pools: Apache DBCP, C3P0, Tomcat JDBC.

**If asked to build one:** keep a `BlockingQueue<Connection>`. `get()` calls `poll(timeout)`, and `release()` calls `offer()`.

**Pool sizing:** bigger isn't better. A small pool (roughly `cores × 2 + disk spindles`) is often faster. Also watch for connection leaks (not closing connections), which show up as pool exhaustion.

---

## 7. ReentrantLock

An explicit lock in `java.util.concurrent.locks`. "Reentrant" means the thread that holds the lock can acquire it again without blocking, and it must unlock the same number of times.

```java
Lock lock = new ReentrantLock();
lock.lock();
try {
    // critical section
} finally {
    lock.unlock();   // always in finally
}
```

**Advantages over `synchronized` (why use it):**
- `tryLock()` and `tryLock(timeout, unit)` avoid indefinite blocking.
- `lockInterruptibly()` lets a waiting thread be interrupted.
- **Fairness:** `new ReentrantLock(true)` grants the lock in FIFO order (slower, but prevents starvation).
- Multiple `Condition` objects (`newCondition()`) for finer wait/notify.
- Introspection: `isLocked()`, `getHoldCount()`, `getQueueLength()`.

**Disadvantages:** you must unlock manually (forgetting causes a permanent lock), and the code is more verbose.

**Related questions:**
- **`synchronized` vs `ReentrantLock`?** `synchronized` is simpler and auto-released. `ReentrantLock` is more flexible.
- **Fair vs unfair?** Unfair (default) has higher throughput, fair avoids starvation.
- **How is it implemented?** Via `AbstractQueuedSynchronizer` (AQS), using CAS on a state variable plus a wait queue.
- **`ReadWriteLock`?** Multiple readers or one writer, good for read-heavy workloads. `StampedLock` adds optimistic reads.

---

## 8. `flush()` and `save()` (Hibernate/JPA)

- **`save()`** makes a transient entity persistent and returns its generated ID. With `IDENTITY` generation, the INSERT runs immediately. Otherwise it's queued in the persistence context. It's Hibernate-specific and deprecated in newer versions. JPA's `persist()` (returns void) is preferred. In Spring Data JPA, `repository.save()` calls `persist()` for new entities and `merge()` for existing ones.
- **`flush()`** synchronizes the persistence context with the database by executing the pending SQL (INSERT/UPDATE/DELETE). It does **not** commit.

**When flush happens automatically:** before transaction commit, and before a query that might be affected by pending changes (FlushMode AUTO).

**Use cases for manual flush:**
- Force constraint violations to surface early.
- Get DB-generated values (triggers, defaults).
- Batch processing: `flush()` then `clear()` every N records to avoid memory bloat.

`saveAndFlush()` in Spring Data does both.

---

## 9. If you use flush, does only committed data reflect in the DB?

**No, flush is not commit.** Flush sends SQL to the database inside the *current transaction*. The changes are visible to the same transaction, but other transactions can't see them (under READ_COMMITTED or higher) until commit. If the transaction rolls back, the flushed changes are undone.

```java
@Transactional
public void demo() {
    repo.save(user);
    repo.flush();          // INSERT executed, but not committed
    throw new RuntimeException();   // rollback, so the row never persists
}
```

Commit implicitly flushes first, but flush doesn't imply commit.

---

## 10. Types of exceptions: checked vs unchecked

```
Throwable
├── Error                  (OutOfMemoryError, StackOverflowError), not meant to be caught
└── Exception
    ├── Checked            (IOException, SQLException, ClassNotFoundException)
    └── RuntimeException   (unchecked: NPE, IllegalArgumentException, ArrayIndexOutOfBounds)
```

| | Checked | Unchecked |
|---|---|---|
| Checked at | Compile time | Runtime |
| Must handle/declare? | Yes | No |
| Typical cause | External/recoverable conditions | Programming bugs |

**Spring tip:** `@Transactional` rolls back by default only for unchecked exceptions and errors. For checked exceptions, use `@Transactional(rollbackFor = Exception.class)`.

---

## 11. Custom exception in Java / Spring Boot

**Plain Java:**
```java
// Checked
public class InsufficientBalanceException extends Exception {
    public InsufficientBalanceException(String message) { super(message); }
}

// Unchecked (more common in Spring)
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) { super(message); }
}
```

**Spring Boot (global handling):**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        ErrorResponse body = new ErrorResponse(404, ex.getMessage(), LocalDateTime.now());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(body);
    }
}
```

Alternatively, annotate the exception with `@ResponseStatus(HttpStatus.NOT_FOUND)`. Spring 6 also supports `ProblemDetail` (RFC 7807).

---

## 12. `throw` vs `throws`

| `throw` | `throws` |
|---|---|
| A **statement** that actually throws an exception object | A **declaration** in the method signature |
| Used inside a method body | Used after the parameter list |
| Followed by one exception instance | Followed by one or more exception **classes** |
| Throws one exception at a time | Can list several, comma-separated |
| `throw new IOException("x");` | `void read() throws IOException, SQLException` |

```java
void withdraw(double amount) throws InsufficientFundsException {   // throws: declares
    if (amount > balance) {
        throw new InsufficientFundsException("Low balance");        // throw: raises
    }
}
```

- `throws` warns callers about **checked exceptions** they must catch or re-declare. Declaring unchecked exceptions is optional.
- `throw` can also re-throw a caught exception: `catch (IOException e) { throw e; }` or wrap it: `throw new ServiceException("msg", e);` (always pass the cause to keep the stack trace).
- An overriding method can't declare broader checked exceptions than the parent method.
- A constructor can use `throws` too.

---

## 13. What if an exception occurs in both the catch and finally blocks?

The exception from the **finally block wins**, and the one from the catch block is lost.

```java
try {
    throw new RuntimeException("try");
} catch (RuntimeException e) {
    throw new IllegalStateException("catch");   // pending
} finally {
    throw new IllegalArgumentException("finally"); // this one propagates
}
// Caller sees: IllegalArgumentException("finally")
```

The `finally` block always runs, and a new exception thrown there replaces the pending one. A `return` in `finally` is worse, because it silently swallows the exception entirely.

**Best practices:**
- Never throw or return from `finally`.
- Use **try-with-resources**, which keeps the primary exception and attaches close-time exceptions via `addSuppressed()`, retrievable with `getSuppressed()`.
- If you must handle manually, catch the `finally` exception and call `primary.addSuppressed(secondary)`.

---

# Batch 2: HashMap, Executors, CompletableFuture, Transactions, GC, Heap

## 1. Why does HashMap allow null keys and values, but ConcurrentHashMap doesn't?

**HashMap:** one null key is allowed (`hash(null)` is 0, so it goes in bucket 0), and any number of null values.

**ConcurrentHashMap:** neither is allowed. The reason is **ambiguity under concurrency**.

```java
String v = map.get("k");   // returns null
```

`null` could mean "key absent" or "key is mapped to null". In a normal `HashMap` you disambiguate with `containsKey("k")`. In a concurrent map, another thread can add or remove `"k"` between your `get()` and `containsKey()`, so that two-step check isn't reliable. Banning nulls removes the ambiguity: `null` from `get()` always means absent. This is also why atomic methods like `putIfAbsent` and `computeIfAbsent` can use `null` as a clean "no mapping" signal.

Other null-hostile maps: `Hashtable`, and `TreeMap` rejects null keys (it needs `compareTo`) but allows null values.

---

## 2. HashMap implementation: how are collisions handled?

(Full answer in Batch 1, Q1.) Quick revision version:

- Each bucket is a `Node<K,V>[]` slot holding a **linked list** (separate chaining).
- On `put`: compute the index, and if the bucket is empty, insert. Otherwise traverse, using `hash` and then `equals()` to detect an existing key (replace its value) or append a new node at the tail.
- **Java 8+:** a chain of 8 or more nodes, with table size at least 64, becomes a **red-black tree** (O(n) to O(log n)). It reverts to a list at 6 or fewer. If the table is under 64, it resizes instead of treeifying.
- **Resize:** at `size > capacity × 0.75`, the capacity doubles. Because capacity is a power of two, each entry either stays at its index or moves to `index + oldCapacity`, so no full rehash is needed.

---

## 3. Hashing algorithm used in HashMap (core Java)

Three steps:

**Step 1:** call `key.hashCode()`.

**Step 2: spread the bits** (Java 8):
```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```
XOR-ing the high 16 bits into the low 16 bits matters because the index uses only the low bits. Without this, keys differing only in the high bits would collide.

**Step 3: pick the bucket:**
```java
index = (n - 1) & hash;   // n = table length, always a power of two
```
This bitwise AND is faster than `%` and equals `hash % n` when `n` is a power of two. That is why `HashMap` always rounds capacity up to a power of two (`new HashMap<>(10)` gives 16).

**`String.hashCode()`:** `s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]`. 31 is an odd prime, and `31 * x` is optimized by the JVM to `(x << 5) - x`. The hash is cached in the String object.

---

## 4. ConcurrentModificationException and IllegalThreadStateException

### ConcurrentModificationException (CME)
Thrown when a collection is **structurally modified while being iterated**, other than through the iterator's own methods. It can happen in a **single thread** too.

How it works: the collection has a `modCount`, and the iterator stores `expectedModCount`. On each `next()`, a mismatch throws CME.

```java
List<Integer> list = new ArrayList<>(List.of(1, 2, 3));
for (Integer i : list) {
    if (i == 2) list.remove(i);   // CME (the classic one)
}
```

**Fixes:**
- `Iterator.remove()`
- `list.removeIf(x -> x == 2)`
- Iterate over a copy
- `CopyOnWriteArrayList` or `ConcurrentHashMap`
- Collect items to remove, then `removeAll`

Note: fail-fast is **best effort**, so it is not guaranteed in unsynchronized multi-threaded code.

### IllegalThreadStateException
Thrown when a thread operation is invalid for the thread's current state. The most common cases:
```java
Thread t = new Thread(() -> {});
t.start();
t.start();          // IllegalThreadStateException (already started)
t.setDaemon(true);  // also throws if called after start()
```
A thread can be started only once. After it reaches TERMINATED, create a new `Thread`.

Don't confuse it with `IllegalMonitorStateException`, which is thrown when `wait()`/`notify()` is called without holding the object's monitor.

---

## 5. ExecutorService: methods and usage

It separates **task submission** from **thread management**, reusing pooled threads instead of creating one per task.

**Hierarchy:** `Executor`, then `ExecutorService`, then `ScheduledExecutorService`.

**Key methods:**

| Method | Purpose |
|---|---|
| `execute(Runnable)` | Fire and forget, no result |
| `submit(Runnable/Callable)` | Returns a `Future` |
| `invokeAll(tasks)` | Runs all, waits for all to complete |
| `invokeAny(tasks)` | Returns the result of one that succeeds |
| `shutdown()` | No new tasks, finishes queued ones |
| `shutdownNow()` | Interrupts running tasks, returns the unstarted ones |
| `awaitTermination(t, unit)` | Blocks until done or timeout |
| `isShutdown()/isTerminated()` | State checks |

**Factory methods (`Executors`):** `newFixedThreadPool(n)`, `newCachedThreadPool()`, `newSingleThreadExecutor()`, `newScheduledThreadPool(n)`, `newWorkStealingPool()`, and in Java 21 `newVirtualThreadPerTaskExecutor()`.

```java
ExecutorService pool = Executors.newFixedThreadPool(10);
try {
    Future<Integer> f = pool.submit(() -> compute());
    Integer result = f.get(5, TimeUnit.SECONDS);
} finally {
    pool.shutdown();
    if (!pool.awaitTermination(30, TimeUnit.SECONDS)) pool.shutdownNow();
}
```

**Callable vs Runnable:** `Callable` returns a value and can throw checked exceptions.

**Production advice:** prefer constructing `ThreadPoolExecutor` directly, with a bounded queue, a named thread factory, and a rejection policy. The `Executors` factories use unbounded queues or unbounded threads. In Spring, use `ThreadPoolTaskExecutor` with `@Async`.

---

## 6. Pool size is 10 and 1000 requests arrive. What happens?

It depends on the pool type.

**`newFixedThreadPool(10)`:** it uses an **unbounded `LinkedBlockingQueue`**. 10 tasks run immediately, and the other **990 wait in the queue** and run as threads free up. Nothing is rejected, but if tasks pile up faster than they finish, you risk **OutOfMemoryError**, plus high latency.

**Custom `ThreadPoolExecutor(core, max, keepAlive, queue, handler)`:** the order is:
1. Fewer than `core` threads: create a new thread.
2. Otherwise: put the task in the **queue**.
3. Queue full and threads below `max`: create an extra thread.
4. Queue full and threads at `max`: apply the **RejectedExecutionHandler**.

Example: core 10, max 20, queue capacity 100. 10 run, 100 queue, 10 more threads spawn (20 running), and the remaining ~870 are **rejected**.

**Rejection policies:**
- `AbortPolicy` (default): throws `RejectedExecutionException`
- `CallerRunsPolicy`: the submitting thread runs it, which creates natural back-pressure
- `DiscardPolicy`: silently drops
- `DiscardOldestPolicy`: drops the oldest queued task

**`newCachedThreadPool()`:** it would try to create up to ~1000 threads, which risks resource exhaustion.

**Good answer:** mention sizing by workload. CPU-bound: about the number of cores. I/O-bound: more threads, or use virtual threads.

---

## 7. What is CompletableFuture and its uses?

Introduced in Java 8. It implements `Future` and `CompletionStage` and lets you build **non-blocking, chained, asynchronous pipelines**.

```java
CompletableFuture<String> cf = CompletableFuture
    .supplyAsync(() -> fetchUser(id), executor)      // run async
    .thenApply(user -> user.getEmail())              // transform
    .thenCompose(email -> fetchOrdersAsync(email))   // chain another async call
    .exceptionally(ex -> "fallback");                // error handling

cf.thenAccept(System.out::println);
```

**Key methods:**
- **Create:** `supplyAsync`, `runAsync`, `completedFuture`
- **Transform:** `thenApply` (like map), `thenCompose` (like flatMap), `thenAccept`, `thenRun`
- **Combine:** `thenCombine` (two independent futures), `allOf`, `anyOf`
- **Errors:** `exceptionally`, `handle`, `whenComplete`
- **Get result:** `join()` (unchecked exception), `get()` (checked)

**Uses:** calling multiple microservices in parallel and merging the results, async DB or REST calls, and reducing response time by running independent work concurrently.

**Gotchas:**
- The default pool is `ForkJoinPool.commonPool()`. Pass your own executor for blocking I/O so you don't starve it.
- `thenApply` vs `thenApplyAsync` differ in which thread runs the callback.
- Always handle exceptions, or they get swallowed silently.

---

## 8. Why did CompletableFuture come into existence?

Because plain `Future` (Java 5) had serious limitations:

1. **`get()` blocks**, so there is no real async.
2. **No callbacks**, so you can't say "when done, do X".
3. **No chaining** of dependent tasks.
4. **No combining** multiple futures (wait for all, or the first).
5. **No exception-handling pipeline.**
6. **Can't be completed manually**, and cancellation is limited.

Without it you ended up with nested blocking calls or "callback hell". `CompletableFuture` gives a functional, composable style, in the spirit of JavaScript Promises. (Reactive libraries like Project Reactor/RxJava go further for streams of data.)

---

## 9. Fail-fast vs fail-safe

| | Fail-fast | Fail-safe (weakly consistent) |
|---|---|---|
| On concurrent modification | Throws `ConcurrentModificationException` | No exception |
| Mechanism | `modCount` check | Iterates over a snapshot or a weakly consistent view |
| Examples | `ArrayList`, `HashMap`, `HashSet` | `CopyOnWriteArrayList`, `ConcurrentHashMap` |
| Sees modifications? | N/A | Snapshot: no. ConcurrentHashMap: may or may not |
| Overhead | Low | Extra memory (copy) or more complex structure |

Note that "fail-safe" is the common interview term. The official Java term is **weakly consistent**. `CopyOnWriteArrayList` iterates over a snapshot copy, and `ConcurrentHashMap` iterators reflect the state at some point and may reflect later updates.

---

## 10. Transactions (understand properly)

A transaction is a unit of work where all operations succeed or none do.

### ACID
- **Atomicity:** all or nothing.
- **Consistency:** moves the DB from one valid state to another.
- **Isolation:** concurrent transactions don't interfere improperly.
- **Durability:** once committed, it survives crashes.

### Isolation levels and the problems they prevent

| Level | Dirty read | Non-repeatable read | Phantom read |
|---|---|---|---|
| READ_UNCOMMITTED | Possible | Possible | Possible |
| READ_COMMITTED | Prevented | Possible | Possible |
| REPEATABLE_READ | Prevented | Prevented | Possible (MySQL InnoDB largely prevents it) |
| SERIALIZABLE | Prevented | Prevented | Prevented |

- **Dirty read:** reading another transaction's uncommitted data.
- **Non-repeatable read:** the same row read twice gives different values.
- **Phantom read:** the same query returns a different set of rows.

### Spring `@Transactional`
- Works via an **AOP proxy**: begin, call the method, commit or rollback.
- **Rollback rule:** by default only for `RuntimeException` and `Error`. Use `rollbackFor = Exception.class` for checked exceptions.
- **Propagation:**
  - `REQUIRED` (default): join the existing transaction or create one
  - `REQUIRES_NEW`: suspend the current one and start a new one
  - `SUPPORTS`, `NOT_SUPPORTED`, `MANDATORY`, `NEVER`
  - `NESTED`: savepoint within the current transaction
- **`readOnly = true`:** optimization hint (e.g. skips dirty checking in Hibernate).

### Common pitfalls (interviewers love these)
- **Self-invocation:** calling a `@Transactional` method from another method in the same class bypasses the proxy, so no transaction is applied.
- `@Transactional` on `private` methods or non-Spring-managed objects does nothing.
- Swallowing the exception in a try/catch means no rollback.
- Long transactions hold locks and connections.

---

## 11. ReentrantLock: why use it?

(Full details in Batch 1, Q7.) Use it when `synchronized` isn't flexible enough:
- **Timed or non-blocking attempts:** `tryLock()`, `tryLock(timeout)`, which helps avoid deadlocks.
- **Interruptible waiting:** `lockInterruptibly()`.
- **Fairness option:** `new ReentrantLock(true)`.
- **Multiple `Condition`s** per lock (like several wait-sets).
- **Introspection:** `isLocked()`, `getHoldCount()`.

Always unlock in `finally`. It is reentrant, so the same thread can lock repeatedly, with a hold count that must be released the same number of times. If you don't need these features, `synchronized` is simpler and safer.

---

## 12. save() vs flush()

(Full details in Batch 1, Q8 and Q9.)

- **`save()`:** makes an entity persistent and schedules the INSERT in the persistence context. With `IDENTITY` ids, the INSERT happens immediately. In Spring Data, it calls `persist()` for new entities and `merge()` for existing ones.
- **`flush()`:** pushes pending changes (SQL) to the DB **within the current transaction**. It does **not** commit, and a rollback still undoes it.
- `saveAndFlush()` does both. Flush also runs automatically before commit and before queries that might be affected.

---

## 13. Garbage Collection

GC automatically reclaims memory from objects that are no longer **reachable**.

**Reachability:** starting from **GC roots** (local variables on thread stacks, static fields, active threads, JNI references), any object not reachable via references is garbage. Circular references are not a problem, unlike reference counting.

**Basic algorithm (mark, sweep, compact):**
1. **Mark:** find live objects.
2. **Sweep:** free dead ones.
3. **Compact:** defragment memory (or copy live objects to another space).

**Generational hypothesis:** most objects die young, so the heap is split by age.
- **Minor GC:** cleans the Young generation. Frequent and fast.
- **Major/Old GC:** cleans the Old generation.
- **Full GC:** cleans the whole heap (plus Metaspace). Slowest, with long stop-the-world pauses.

**Stop-the-world (STW):** application threads pause during parts of GC.

**Collectors:**

| Collector | Notes |
|---|---|
| Serial | Single-threaded, small apps |
| Parallel | Throughput-focused, default in Java 8 |
| CMS | Low pause, deprecated in 9, removed in 14 |
| **G1** | Default since Java 9. Region-based, predictable pauses (`-XX:MaxGCPauseMillis`) |
| **ZGC** | Very low pause (sub-millisecond to few ms), huge heaps, concurrent |
| Shenandoah | Low pause, concurrent compaction |

**Useful flags:** `-Xms`, `-Xmx`, `-XX:+UseG1GC`, `-Xlog:gc*` (Java 9+; `-XX:+PrintGCDetails` on Java 8).

**Other points:**
- `System.gc()` is only a **hint**, not a guarantee.
- `finalize()` is deprecated. Use `try-with-resources` or `Cleaner`.
- **Memory leaks in Java:** unreleased static collections, unclosed resources, listeners never removed, `ThreadLocal` not cleaned up, bad `equals`/`hashCode` keys in maps.
- **Tools:** VisualVM, JConsole, `jmap`, heap dump with Eclipse MAT.

---

## 14. Heap memory structure

```
JVM Memory
├── Heap (shared, GC-managed)
│   ├── Young Generation
│   │   ├── Eden          (new objects allocated here)
│   │   ├── Survivor S0
│   │   └── Survivor S1
│   └── Old (Tenured) Generation   (long-lived objects)
│
├── Metaspace  (native memory: class metadata, replaced PermGen in Java 8)
│
└── Per-thread areas
    ├── Stack (frames, local variables, references)
    ├── PC register
    └── Native method stack
```

**Object lifecycle:**
1. New objects are allocated in **Eden**.
2. When Eden fills, a **Minor GC** runs. Live objects move to a Survivor space (S0 and S1 alternate as to-space and from-space) and their **age** increments.
3. After surviving enough cycles (the tenuring threshold, up to 15), the object is **promoted to Old Gen**.
4. Very large objects may be allocated directly in Old Gen (in G1, as "humongous" objects).
5. When Old Gen fills, a Major/Full GC runs.

**Heap vs Stack:**
- **Heap:** objects, shared between threads, GC-managed.
- **Stack:** one per thread, holds primitives and references in method frames, and is freed automatically when the method exits. Overflow gives `StackOverflowError`.

**PermGen vs Metaspace:** PermGen had a fixed size, giving `OutOfMemoryError: PermGen space`. Metaspace uses native memory and grows dynamically (cap it with `-XX:MaxMetaspaceSize`).

**Common OOM errors:** `Java heap space`, `Metaspace`, `GC overhead limit exceeded`, and `unable to create new native thread`.

**Tuning flags:** `-Xms` (initial heap), `-Xmx` (max heap), `-Xmn` (young size), `-XX:SurvivorRatio`.

---

# Batch 3: GC Control, Records, Interfaces, ExecutorService

## 1. Can we delay garbage collection or the expiration of objects?

You can't directly *control* GC, but you can **keep objects alive longer** or **influence when GC runs**.

**Keeping objects alive (the real answer):**
- An object is collected only when it's unreachable. A **strong reference** (the normal kind) from a GC root keeps it alive, for example via a `static` field, a cache, or a long-lived collection.
- **Reference types** in `java.lang.ref` give you finer control:

| Type | Collected when | Typical use |
|---|---|---|
| Strong | Never while reachable | Normal references |
| `SoftReference` | Only when memory is low (before OOM) | Memory-sensitive caches |
| `WeakReference` | At the next GC | `WeakHashMap`, canonical mappings |
| `PhantomReference` | After finalization, used with `ReferenceQueue` | Cleanup actions (replaces `finalize`) |

**Influencing GC timing and behavior:**
- **Bigger heap:** `-Xms` / `-Xmx` means GC runs less often.
- **Bigger Young generation:** `-Xmn` or `-XX:NewRatio` means fewer minor GCs and more time for objects to die.
- **Tenuring:** `-XX:MaxTenuringThreshold` controls how many GC cycles an object survives before promotion to Old Gen.
- **Choose a different collector:** G1 with `-XX:MaxGCPauseMillis`, or ZGC/Shenandoah for low pauses.
- `System.gc()` only *requests* GC (and can be disabled with `-XX:+DisableExplicitGC`). It cannot *delay* it.

**"Expiration of objects" in a caching context:**
Java has no built-in TTL. Use a cache library such as **Caffeine** or **Guava Cache** (`expireAfterWrite`, `expireAfterAccess`), or **Spring Cache** with a provider like Caffeine or Redis (`@Cacheable` plus TTL config).

```java
Cache<String, User> cache = Caffeine.newBuilder()
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .maximumSize(1000)
    .build();
```

**Caution to mention:** don't try to fight GC. Holding objects too long causes memory leaks and `OutOfMemoryError`. Tune only after measuring with GC logs.

---

## 2. What is a record in Java?

A **record** (finalized in Java 16) is a concise way to declare an **immutable data carrier** class.

```java
public record Person(String name, int age) {}
```

The compiler automatically generates:
- A `private final` field for each component
- A **canonical constructor**
- **Accessor methods** named like the component: `name()`, `age()` (not `getName()`)
- `equals()`, `hashCode()`, `toString()`, based on all components

**Characteristics:**
- Implicitly `final` (can't be extended), and extends `java.lang.Record`
- Can **implement interfaces**, but can't extend classes
- Can have **static fields and methods**, and instance methods
- **Cannot** declare additional instance fields (only the components)
- Shallowly immutable: fields can't be reassigned, but a mutable object inside (a `List`, say) can still be modified

**Use cases:** DTOs, API request/response models, value objects, map keys, multi-value return types, pattern matching (Java 21 record patterns).

---

## 3. Can a record have a constructor?

**Yes.** Three forms:

**a) Compact canonical constructor** (for validation or normalization). No parameter list, and fields are assigned automatically at the end:
```java
public record Person(String name, int age) {
    public Person {
        if (age < 0) throw new IllegalArgumentException("age < 0");
        name = name.trim();   // reassign the parameter, not the field
    }
}
```

**b) Explicit canonical constructor.** You write the full parameter list and must assign every field yourself:
```java
public record Person(String name, int age) {
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

**c) Non-canonical (custom) constructor.** It must delegate to another constructor with `this(...)`:
```java
public record Person(String name, int age) {
    public Person(String name) {
        this(name, 0);
    }
}
```

**Rules:**
- A canonical constructor must be at least as accessible as the record itself.
- Inside a compact constructor you can't write `this.name = ...`.
- Defensive copy for mutable components goes here: `items = List.copyOf(items);`

---

## 4. Can an interface have an interface?

**Yes, an interface can contain a nested interface** (member interface). It is implicitly `public` and `static`.

```java
interface Outer {
    void doWork();

    interface Inner {          // implicitly public static
        void helper();
    }
}

class Impl implements Outer.Inner {
    public void helper() {}
}
```

**Also true, and often confused with this:** an interface can **extend** other interfaces, even multiple ones:
```java
interface C extends A, B {}
```

**Related facts:**
- Interfaces can also nest classes, enums, and records (all implicitly `public static`).
- Since Java 8: `default` and `static` methods. Since Java 9: `private` methods.
- A class can implement multiple interfaces, which is how Java supports multiple inheritance of type.
- If two interfaces have the same default method, the implementing class must override it to resolve the conflict (`A.super.hello()`).

---

## 5. How can you make a record mutable?

**Short answer: you can't, by design.** Record component fields are always `private final`, so they cannot be reassigned.

**What you can do:**

**a) Hold a mutable object inside it** (shallow immutability):
```java
public record Team(String name, List<String> members) {}

var t = new Team("A", new ArrayList<>());
t.members().add("Bob");    // allowed, the list itself is mutable
```
The record's *reference* is fixed, but the object it points to can change. This is usually a pitfall. Prevent it with a defensive copy:
```java
public record Team(String name, List<String> members) {
    public Team {
        members = List.copyOf(members);   // now immutable
    }
}
```
Also return copies from accessors if needed.

**b) Add static mutable state** (not instance state). It's allowed but is shared across all instances, so rarely useful.

**c) "Withers" (the correct pattern for "changing" a record):** create a new instance with the modified value.
```java
public record Person(String name, int age) {
    public Person withAge(int newAge) { return new Person(name, newAge); }
}
```

**If you need real mutability, use a regular class** (with Lombok `@Data` if you want less boilerplate). Records are intentionally for immutable data. Also note that JPA entities generally can't be records, because Hibernate needs a no-arg constructor and non-final fields. Records work well for DTOs and projections.

---

## 6. Multithreading: ExecutorService (consolidated)

**Why use it:**
- Creating a thread per task is expensive and unbounded. A **thread pool** reuses threads and limits concurrency.
- It decouples *what* to run (the task) from *how* to run it (the threading policy).

**Core pieces:**
- `Runnable` (no result) vs `Callable<V>` (returns a value, can throw checked exceptions)
- `Future<V>`: `get()`, `get(timeout)`, `cancel()`, `isDone()`
- `Executors` factories, or a custom `ThreadPoolExecutor`

**`ThreadPoolExecutor` parameters:** `corePoolSize`, `maximumPoolSize`, `keepAliveTime`, `workQueue`, `threadFactory`, `handler` (rejection policy).

**Task flow:** core threads first, then queue, then extra threads up to max, then the rejection policy.

**Practical example (parallel calls):**
```java
ExecutorService pool = Executors.newFixedThreadPool(4);
try {
    List<Callable<String>> tasks = List.of(
        () -> callServiceA(),
        () -> callServiceB(),
        () -> callServiceC());

    for (Future<String> f : pool.invokeAll(tasks)) {
        System.out.println(f.get());
    }
} finally {
    pool.shutdown();
}
```

**Points that impress interviewers:**
- Always **shut down** the pool (`shutdown()` then `awaitTermination()`), otherwise non-daemon threads keep the JVM alive.
- `execute()` with a task that throws kills the worker thread (the pool replaces it) and the exception goes to the uncaught handler. `submit()` captures the exception inside the `Future`, and you only see it on `get()` (as `ExecutionException`).
- Avoid the unbounded-queue risk of `newFixedThreadPool` in production, and name your threads for easier debugging.
- Avoid deadlock by not submitting tasks that wait on other tasks in the same bounded pool.
- **Java 21:** `Executors.newVirtualThreadPerTaskExecutor()` suits I/O-heavy workloads.
- In Spring Boot: `ThreadPoolTaskExecutor` with `@Async` and `@EnableAsync`.

**Likely follow-ups:**
- `submit()` vs `execute()`?
- `shutdown()` vs `shutdownNow()`?
- How do you choose the pool size? (CPU-bound: about the number of cores. I/O-bound: cores × (1 + wait time / compute time).)
- `ExecutorService` vs `ForkJoinPool`? (Work stealing, suited to divide-and-conquer.)
- `ExecutorService` vs `CompletableFuture`? (CompletableFuture adds non-blocking chaining on top of an executor.)

---

# Batch 4: Quick Repeats

These three questions were asked again. The full answers are above; here are the concise versions.

## 1. What is method hiding?

When a **subclass defines a `static` method with the same signature as a static method in its parent**, the child's method **hides** the parent's. It is not overriding, because static methods belong to the class, not the object. Hiding is resolved at compile time by the **reference type**, while overriding is resolved at runtime by the **actual object type**. Fields are hidden too. See Batch 1, Q2 for the code example and rules.

## 2. What is CompletableFuture?

A Java 8 class implementing `Future` and `CompletionStage` for **non-blocking, chained, asynchronous code** (`supplyAsync`, `thenApply`, `thenCompose`, `thenCombine`, `allOf`, `exceptionally`). It exists because plain `Future` has no callbacks, chaining, combining or exception pipeline, and `get()` blocks. Pass your own executor for blocking I/O and always handle exceptions. See Batch 2, Q7 and Q8.

## 3. What is `throw` and what is `throws`?

`throw` is a **statement** that raises an exception object inside a method. `throws` is a **declaration** in the method signature listing checked exceptions the caller must handle. See Batch 1, Q12.

---

# Batch 5: Spring Boot

## 1. `@PathVariable` vs `@RequestParam` vs `@ResponseBody`

| Annotation | Reads/writes | Example URL | Typical use |
|---|---|---|---|
| `@PathVariable` | Value **in the URL path** | `/users/42` | Identify a specific resource |
| `@RequestParam` | **Query string** or form data | `/users?role=admin&page=1` | Filtering, sorting, pagination, optional inputs |
| `@RequestBody` | **HTTP request body** (JSON to object) | POST/PUT payload | Create/update with a complex object |
| `@ResponseBody` | **Response body** (object to JSON) | N/A | Return data instead of a view name |

```java
@GetMapping("/users/{id}")
public User get(@PathVariable Long id,
                @RequestParam(defaultValue = "false") boolean details) { ... }

@PostMapping("/users")
public User create(@RequestBody @Valid UserDto dto) { ... }
```

**Points to mention:**
- `@RequestParam` supports `required=false` and `defaultValue`. `@PathVariable` is required by default (`required=false` is possible).
- `@ResponseBody` makes Spring use an `HttpMessageConverter` (Jackson) to serialize the return value. `@RestController` = `@Controller` + `@ResponseBody`.
- `ResponseEntity<T>` gives control over status code and headers too.
- Use `@Valid` on `@RequestBody` for validation.

---

## 2. Types of scope and their uses (Spring bean scopes)

| Scope | Meaning | Use |
|---|---|---|
| **singleton** (default) | One instance per Spring container | Stateless services, repositories, config |
| **prototype** | New instance every time it is requested | Stateful, non-shareable objects |
| **request** | One per HTTP request | Per-request data (web only) |
| **session** | One per HTTP session | User session data, cart |
| **application** | One per `ServletContext` | App-wide shared data in web apps |
| **websocket** | One per WebSocket session | WebSocket state |

```java
@Component
@Scope("prototype")
public class ReportBuilder { }

@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST, proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestContext { }
```

**Interview gotchas:**
- Singleton is **per container, not per JVM**, and singleton beans must be **thread-safe** (keep them stateless).
- **Prototype injected into a singleton** is created only once, at injection time. Fix with `ObjectProvider<T>`, `@Lookup`, or a scoped proxy.
- Spring does **not** manage the full lifecycle of prototype beans (no `@PreDestroy` call).
- Request/session beans injected into singletons need a **scoped proxy**.

*(If they meant Java variable scope: local, instance, static/class, and block scope.)*

---

## 3. Global exception handling

Use `@RestControllerAdvice` (`@ControllerAdvice` + `@ResponseBody`) with `@ExceptionHandler` methods to handle exceptions from all controllers in one place.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ApiError> notFound(ResourceNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                .body(new ApiError(404, ex.getMessage(), Instant.now()));
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ApiError> validation(MethodArgumentNotValidException ex) {
        String msg = ex.getBindingResult().getFieldErrors().stream()
                .map(e -> e.getField() + ": " + e.getDefaultMessage())
                .collect(Collectors.joining(", "));
        return ResponseEntity.badRequest().body(new ApiError(400, msg, Instant.now()));
    }

    @ExceptionHandler(Exception.class)   // fallback
    public ResponseEntity<ApiError> generic(Exception ex) {
        log.error("Unexpected error", ex);
        return ResponseEntity.status(500).body(new ApiError(500, "Internal error", Instant.now()));
    }
}
```

**Also mention:** `@ResponseStatus` on an exception class, `ResponseStatusException`, `ProblemDetail` (RFC 7807, Spring 6), and extending `ResponseEntityExceptionHandler`. Never leak stack traces to clients. Log server-side. The most specific handler wins.

---

## 4. Filter vs Interceptor

| | **Filter** | **Interceptor** |
|---|---|---|
| Belongs to | Servlet spec (`jakarta.servlet.Filter`) | Spring MVC (`HandlerInterceptor`) |
| Runs | **Before** the request reaches `DispatcherServlet` | After `DispatcherServlet`, around the **controller** |
| Access to | Raw request/response | Handler method, `ModelAndView`, Spring context |
| Typical use | Security (JWT/auth), CORS, logging, compression, request wrapping | Authorization checks, audit, timing, adding common model data |
| Registered via | `@Component`/`FilterRegistrationBean` | `WebMvcConfigurer.addInterceptors()` |

**Flow:** Client → Filter → DispatcherServlet → Interceptor `preHandle` → Controller → `postHandle` → view/response → `afterCompletion` → Filter (on the way out).

```java
@Component
public class JwtFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest req, HttpServletResponse res, FilterChain chain)
            throws ServletException, IOException {
        // validate token...
        chain.doFilter(req, res);
    }
}

@Component
public class AuditInterceptor implements HandlerInterceptor {
    public boolean preHandle(HttpServletRequest req, HttpServletResponse res, Object handler) {
        return true;   // false stops the chain
    }
    public void postHandle(HttpServletRequest req, HttpServletResponse res, Object handler, ModelAndView mv) { }
    public void afterCompletion(HttpServletRequest req, HttpServletResponse res, Object handler, Exception ex) { }
}

@Configuration
public class WebConfig implements WebMvcConfigurer {
    @Autowired AuditInterceptor audit;
    public void addInterceptors(InterceptorRegistry r) { r.addInterceptor(audit).addPathPatterns("/api/**"); }
}
```

**Remember:** exceptions thrown in a filter are **not** caught by `@ControllerAdvice` (they occur before the DispatcherServlet). Use `OncePerRequestFilter` to guarantee one execution per request. Control filter order with `@Order`.

---

## 5. End-to-end endpoint example

```java
// Entity
@Entity
public class Product {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private BigDecimal price;
    // getters/setters
}

// DTO (record)
public record ProductDto(
    Long id,
    @NotBlank String name,
    @Positive BigDecimal price) {}

// Repository
public interface ProductRepository extends JpaRepository<Product, Long> {}

// Service
@Service
@RequiredArgsConstructor
public class ProductService {
    private final ProductRepository repo;

    @Transactional
    public ProductDto create(ProductDto dto) {
        Product p = new Product();
        p.setName(dto.name());
        p.setPrice(dto.price());
        Product saved = repo.save(p);
        return new ProductDto(saved.getId(), saved.getName(), saved.getPrice());
    }

    @Transactional(readOnly = true)
    public ProductDto get(Long id) {
        Product p = repo.findById(id)
            .orElseThrow(() -> new ResourceNotFoundException("Product " + id + " not found"));
        return new ProductDto(p.getId(), p.getName(), p.getPrice());
    }
}

// Controller
@RestController
@RequestMapping("/api/products")
@RequiredArgsConstructor
public class ProductController {
    private final ProductService service;

    @PostMapping
    public ResponseEntity<ProductDto> create(@Valid @RequestBody ProductDto dto) {
        ProductDto created = service.create(dto);
        URI location = URI.create("/api/products/" + created.id());
        return ResponseEntity.created(location).body(created);   // 201
    }

    @GetMapping("/{id}")
    public ProductDto get(@PathVariable Long id) {
        return service.get(id);
    }
}
```

Plus the `GlobalExceptionHandler` from Q3 and `application.properties` for the datasource.

**Talking points:** layered architecture (controller, service, repository), DTOs instead of exposing entities, constructor injection, validation, correct status codes (201 and `Location` on create), and transactions in the service layer, not the controller.

---

## 6. Async communication in Spring Boot

Two meanings. Cover both.

**A) Async execution inside the app**
```java
@Configuration @EnableAsync
public class AsyncConfig {
    @Bean
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor ex = new ThreadPoolTaskExecutor();
        ex.setCorePoolSize(5); ex.setMaxPoolSize(10); ex.setQueueCapacity(100);
        ex.setThreadNamePrefix("async-");
        ex.initialize();
        return ex;
    }
}

@Service
public class EmailService {
    @Async("taskExecutor")
    public CompletableFuture<Void> sendEmail(String to) { ... return CompletableFuture.completedFuture(null); }
}
```
- `@Async` works through a **proxy**, so self-invocation doesn't work and the method must be `public`.
- Return `void`, `Future` or `CompletableFuture`. Exceptions in `void` methods go to `AsyncUncaughtExceptionHandler`.
- `CompletableFuture` can also be used directly for parallel service calls.

**B) Async communication between services**
- **Message brokers:** Kafka, RabbitMQ, ActiveMQ, AWS SQS. Use `KafkaTemplate`/`@KafkaListener` or `RabbitTemplate`/`@RabbitListener`. This gives decoupling, retries, and a buffer against load spikes.
- **Non-blocking HTTP:** `WebClient` (Spring WebFlux) returning `Mono`/`Flux`.
- **Spring events:** `ApplicationEventPublisher` + `@EventListener` (add `@Async`, or `@TransactionalEventListener` to fire after commit) for in-process decoupling.
- **Reliability patterns:** idempotent consumers, dead-letter queues, the **outbox pattern** (write the event in the same DB transaction, publish later), and the **saga pattern** for distributed transactions.

---

## 7. Filter, interceptor, callbacks/annotations in Spring Boot

You may have meant lifecycle callbacks. Here is the quick map:

| Type | Mechanism |
|---|---|
| **Bean init callback** | `@PostConstruct`, `InitializingBean.afterPropertiesSet()`, `@Bean(initMethod=...)` |
| **Bean destroy callback** | `@PreDestroy`, `DisposableBean.destroy()` |
| **Startup hooks** | `CommandLineRunner`, `ApplicationRunner`, `@EventListener(ApplicationReadyEvent.class)` |
| **Interceptor callbacks** | `preHandle`, `postHandle`, `afterCompletion` |
| **Filter** | `doFilter` / `doFilterInternal` |
| **AOP advice** | `@Before`, `@After`, `@AfterReturning`, `@AfterThrowing`, `@Around` |
| **Exception callback** | `@ExceptionHandler`, `@ControllerAdvice` |
| **Transaction callbacks** | `@TransactionalEventListener(phase = AFTER_COMMIT)` |
| **JPA entity callbacks** | `@PrePersist`, `@PostPersist`, `@PreUpdate`, `@PreRemove`, `@PostLoad` |
| **Others** | `@Scheduled`, `@EventListener`, `BeanPostProcessor` |

**Bean lifecycle order:** instantiate, inject dependencies, `Aware` callbacks, `BeanPostProcessor.before`, `@PostConstruct`, `afterPropertiesSet`, init-method, `BeanPostProcessor.after` (proxies are created here), bean ready, then `@PreDestroy` / `destroy` on shutdown.

---

## 8. Transactions and annotation detail

`@Transactional` (from `org.springframework.transaction.annotation`) can go on a class or a public method.

| Attribute | Purpose |
|---|---|
| `propagation` | How it behaves relative to an existing transaction |
| `isolation` | DEFAULT, READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE |
| `readOnly` | Hint for optimization (skips dirty checking in Hibernate) |
| `timeout` | Seconds before the transaction is rolled back |
| `rollbackFor` / `rollbackForClassName` | Extra exceptions that trigger rollback |
| `noRollbackFor` | Exceptions that should **not** trigger rollback |
| `transactionManager` | Choose a specific manager (multi-datasource) |

**Propagation:**
- `REQUIRED` (default): join the existing one, or create one.
- `REQUIRES_NEW`: suspend the current one and start a fully independent one (e.g. audit logs that must persist even if the outer one fails).
- `SUPPORTS`: use one if it exists, otherwise run non-transactionally.
- `NOT_SUPPORTED`: suspend and run without a transaction.
- `MANDATORY`: must already be in a transaction, otherwise throws.
- `NEVER`: throws if a transaction exists.
- `NESTED`: savepoint inside the current transaction.

**Pitfalls:** self-invocation, private/final methods, swallowed exceptions, long transactions, and lazy loading outside a transaction (`LazyInitializationException`).

---

## 9. What is a Spring proxy?

Spring implements cross-cutting features (`@Transactional`, `@Async`, `@Cacheable`, `@Secured`, AOP) by wrapping your bean in a **proxy object**. Callers get the proxy, which runs extra logic (begin transaction, commit/rollback) around the call to your real method.

**Two proxy types:**
- **JDK dynamic proxy:** used when the bean implements an interface and the proxy implements the same interface.
- **CGLIB proxy:** creates a **subclass** of the bean. This is the default in Spring Boot (`proxyTargetClass=true`). Classes and methods must not be `final`.

**Why it matters:**
- **Self-invocation bypasses the proxy**, so `this.methodB()` inside the same class ignores `@Transactional`/`@Async` on `methodB`. Fixes: move the method to another bean, inject self, or use `AopContext.currentProxy()`.
- Only **public** methods are proxied (by default).
- Proxies are created in `BeanPostProcessor.postProcessAfterInitialization`.

---

## 10. If a transaction fails, how do you handle it?

**1. Rollback:** Spring rolls back automatically for the right exception types (see Q11).

**2. Catch and translate:** convert to a meaningful business exception and return a proper HTTP response via `@RestControllerAdvice`. Log with context.

**3. Retry (for transient failures like deadlocks or optimistic lock conflicts):**
```java
@Retryable(retryFor = {OptimisticLockingFailureException.class, CannotAcquireLockException.class},
           maxAttempts = 3, backoff = @Backoff(delay = 200))
@Transactional
public void update(...) { ... }
```
Retry must wrap the transaction (each attempt gets a new transaction). Use Spring Retry or Resilience4j.

**4. Isolate side effects:** use `@TransactionalEventListener(phase = AFTER_COMMIT)` so emails and messages are sent only if the commit succeeds. Use `REQUIRES_NEW` for audit or failure logging that must survive the rollback.

**5. Distributed systems:** you can't use one DB transaction across services, so use the **Saga pattern** (compensating actions), the **Outbox pattern**, **idempotency keys**, and dead-letter queues.

**6. Monitor:** alerts, metrics, and logging of the failure.

---

## 11. When does a transaction fail/roll back?

**Default rule:** Spring rolls back on **unchecked exceptions** (`RuntimeException` and subclasses) and **`Error`**. It does **not** roll back on **checked exceptions**, and it commits instead.

**Situations where it fails or rolls back:**
- `RuntimeException`: NPE, `IllegalArgumentException`, `DataIntegrityViolationException`, etc.
- `Error` (e.g. `OutOfMemoryError`).
- DB problems: constraint violation, deadlock, timeout, connection lost.
- Optimistic locking failure (`OptimisticLockingFailureException`).
- Commit-time failures (e.g. a deferred constraint).
- **`UnexpectedRollbackException`:** an inner `REQUIRED` method throws a runtime exception and marks the shared transaction rollback-only. The outer method catches the exception and tries to commit, and Spring throws `UnexpectedRollbackException`.

**Why a transaction might *not* roll back when you expect it to:**
- Checked exception thrown (default).
- Exception caught and swallowed inside the method.
- Self-invocation, or a `private` method, so there's no proxy.
- Bean not managed by Spring (created with `new`).
- Wrong `@Transactional` import (`jakarta.transaction.Transactional` has different rules from Spring's).
- Non-transactional storage engine (e.g. MyISAM).
- Exception thrown in an `@Async` thread, which runs in a different transaction.

---

## 12. If a transaction fails for a checked exception, how do you handle it?

Because Spring commits on checked exceptions by default, you have these options:

**Option 1: `rollbackFor`** (most common)
```java
@Transactional(rollbackFor = Exception.class)
public void process() throws IOException { ... }
```

**Option 2: Wrap in an unchecked exception**
```java
try { ... } catch (IOException e) {
    throw new ServiceException("failed", e);
}
```

**Option 3: Mark rollback programmatically**
```java
try { ... } catch (IOException e) {
    TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();
    // handle gracefully, return a result, no exception propagates
}
```

**Option 4: Programmatic transactions** with `TransactionTemplate` for full control.

**Opposite direction:** `noRollbackFor = SomeBusinessException.class` to commit despite a runtime exception.

Many teams make business exceptions unchecked (extend `RuntimeException`) so the default behavior just works. Alternatively, apply `rollbackFor = Exception.class` as a convention.

---

## 13. Can a service layer contain a controller? What happens?

This question can be read two ways, so cover both in the interview.

**A) Injecting a controller into a service (calling a controller from a service)**
- **Technically works**, because controllers are normal Spring beans.
- **But it's bad design.** It breaks the layering (Controller → Service → Repository). Controllers deal with HTTP concerns (`HttpServletRequest`, `ResponseEntity`, status codes), and the service layer should be HTTP-agnostic and reusable from schedulers, message consumers, and so on.
- **Circular dependency:** the controller depends on the service and the service on the controller. Since **Spring Boot 2.6**, circular references are **prohibited by default**, so the app fails at startup with `BeanCurrentlyInCreationException` / `UnsatisfiedDependencyException` (unless `spring.main.allow-circular-references=true`, which is only a band-aid).
- **Better approach:** extract the shared logic into a service (or helper) that both use. If one service needs another, inject that service, not a controller.

**B) Annotating a class in the service layer with `@Controller`/`@RestController`**
- Component scan registers it as a bean, and Spring MVC maps its `@RequestMapping` methods, so the service accidentally **exposes HTTP endpoints**.
- Transaction and proxy behavior still follows the annotations, but the separation of concerns is lost, which makes testing, security, and maintenance harder (e.g. security rules scoped to controller packages are skipped).

**Related points to mention:**
- Each layer has one responsibility: Controller (HTTP, validation, mapping), Service (business logic, transactions), Repository (data access).
- Put `@Transactional` on the **service**, not the controller, so the transaction boundary matches the business operation.
- Don't pass `HttpServletRequest` or `ResponseEntity` into services. Pass plain values or DTOs.

---

# Batch 6: Kafka

## 1. What is Kafka?

Apache Kafka is a **distributed, fault-tolerant, high-throughput event streaming platform**. Producers write messages to **topics**, and consumers read from them. Messages are stored durably on disk as an **append-only log** and are retained for a configured time (default 7 days), not deleted on consumption.

**Core terms:**
- **Producer:** publishes records to a topic.
- **Consumer:** reads records, usually as part of a **consumer group**.
- **Broker:** a Kafka server. A set of brokers forms a **cluster**.
- **Topic:** a named stream of records, split into partitions.
- **Offset:** a record's sequential position within a partition. Consumers track their own progress by offset.
- **Controller:** a broker that manages cluster metadata and leader election (**KRaft** in newer versions, replacing ZooKeeper).

**Uses:** async communication between microservices, event-driven architecture, log aggregation, real-time analytics, change data capture, and decoupling producers from consumers.

---

## 2. What is a Partition?

A topic is divided into one or more **partitions**. Each partition is an **ordered, immutable sequence of records**, each with an offset.

```
Topic "orders" (3 partitions)
P0: [0][1][2][3]...
P1: [0][1][2]...
P2: [0][1][2][3][4]...
```

**Why partitions exist:**
- **Scalability:** a topic's data is spread across multiple brokers, so no single machine limits throughput.
- **Parallelism:** in a consumer group, **each partition is consumed by exactly one consumer** at a time. More partitions means more consumers can work in parallel.

**How a record picks its partition:**
- With a **key:** `hash(key) % numPartitions`, so the same key always goes to the same partition.
- Without a key: spread across partitions (sticky/round-robin).
- Or a custom `Partitioner`.

**Ordering guarantee:** Kafka guarantees order **only within a partition**, not across the whole topic. That's why you use a key (like `orderId` or `userId`) when related events must stay in order.

**Key points:**
- Max useful consumers in a group equals the number of partitions. Extra consumers sit idle.
- You can **increase** partitions later but never decrease them, and increasing changes the key-to-partition mapping.
- Too many partitions means more open files, more memory, and slower leader election.

---

## 3. What are Replicas?

A **replica** is a copy of a partition stored on a different broker. Replication provides **fault tolerance**.

- **Leader replica:** one per partition. **All reads and writes go through the leader** (by default).
- **Follower replicas:** continuously fetch data from the leader to stay in sync. They don't serve clients normally.
- **ISR (In-Sync Replicas):** the set of replicas (including the leader) that are fully caught up with the leader. Only an ISR member can be elected as the new leader.

```
Partition P0, replication factor 3
Broker 1: P0 (Leader)
Broker 2: P0 (Follower)
Broker 3: P0 (Follower)
```

Replicas of the same partition are always placed on **different brokers**, so one broker failure never loses all copies.

---

## 4. What happens if a broker goes down?

Assume replication factor 3 and a healthy cluster.

1. The controller detects the broker failure (via a missed heartbeat/session timeout).
2. **For partitions where that broker was a follower:** nothing breaks. The partition keeps working, and the ISR shrinks by one.
3. **For partitions where that broker was the leader:** the controller **elects a new leader from the ISR**. Producers and consumers refresh metadata and switch to the new leader, usually within seconds, with no data loss for acknowledged writes.
4. When the broker comes back, its replicas **catch up** from the current leaders and rejoin the ISR. Leadership can be rebalanced back (`auto.leader.rebalance.enable`, preferred leader election).

**When data loss or unavailability can happen:**
- If **all ISR members are down**, the partition is unavailable. With `unclean.leader.election.enable=true` an out-of-sync replica can become leader, which is available but may lose data. The default is `false`, favoring consistency.
- If ISR size drops below `min.insync.replicas` and producers use `acks=all`, writes are **rejected** (`NotEnoughReplicasException`) until enough replicas are back.

**Consumer side:** if a consumer dies, the group **rebalances** and its partitions are reassigned to the remaining consumers, which resume from the last committed offset.

---

## 5. What does "replication factor 3" mean?

`replication-factor=3` means **each partition has 3 copies in total (1 leader + 2 followers)**, each on a different broker. It requires **at least 3 brokers** in the cluster.

```bash
kafka-topics.sh --create --topic orders \
  --partitions 3 --replication-factor 3 \
  --bootstrap-server localhost:9092
```

**Fault tolerance:** with RF=3 the cluster tolerates the loss of **up to 2 brokers** without losing data (for that partition, with proper settings). With RF=1 there's no redundancy, and one broker failure means data loss.

**Total copies** = partitions × replication factor. For example, 3 partitions × RF 3 = 9 partition replicas spread across the brokers.

### The settings that go with it (very commonly asked together)

| Setting | Meaning |
|---|---|
| `acks=0` | Producer doesn't wait. Fastest, may lose data |
| `acks=1` | Waits for the leader only. Data can be lost if the leader fails before replication |
| `acks=all` (`-1`) | Waits for all **in-sync** replicas. Strongest durability |
| `min.insync.replicas` | Minimum ISR size required for a write to succeed with `acks=all` |

**The classic production setup:** `replication.factor=3`, `min.insync.replicas=2`, `acks=all`. This tolerates **one broker failure** while still accepting writes, and guarantees no acknowledged data loss. If two brokers fail, writes stop (but reads and data stay safe).

**Trade-offs:** higher RF means more durability but more disk, network usage, and write latency.

---

## Likely follow-up questions (Kafka)

- **Partition vs replica?** Partitions split data for **scalability**. Replicas copy data for **fault tolerance**.
- **Can the number of consumers exceed partitions?** Yes, but the extras stay idle.
- **How do you guarantee ordering?** Use the same key, so the same partition, and a single consumer per partition. For producers, set `enable.idempotence=true` and `max.in.flight.requests.per.connection<=5`.
- **What is a consumer group?** Consumers sharing a `group.id` split the partitions between them. Different groups each get the full stream independently.
- **How are offsets committed?** Auto-commit or manual (`commitSync`/`commitAsync`). Offsets are stored in the internal `__consumer_offsets` topic.
- **Delivery semantics?** At-most-once, at-least-once (default, may duplicate, so make consumers idempotent), and exactly-once (idempotent producer plus transactions).
- **Why is Kafka fast?** Sequential disk I/O, the OS page cache, zero-copy transfer, batching, and compression.
- **Spring Boot usage:** `KafkaTemplate.send(topic, key, value)` and `@KafkaListener(topics = "orders", groupId = "order-service")`.

```java
@Service
public class OrderProducer {
    private final KafkaTemplate<String, String> kafka;
    public OrderProducer(KafkaTemplate<String, String> kafka) { this.kafka = kafka; }

    public void send(String orderId, String payload) {
        kafka.send("orders", orderId, payload);   // key = orderId keeps order per customer/order
    }
}

@Component
public class OrderConsumer {
    @KafkaListener(topics = "orders", groupId = "order-service")
    public void consume(String message) { /* process idempotently */ }
}
```

---

# Batch 7: Spring Security and JWT

## 1. Authentication vs Authorization

| | Authentication | Authorization |
|---|---|---|
| Question | **Who are you?** | **What are you allowed to do?** |
| Happens | First | After authentication |
| Mechanism | Username/password, JWT, OAuth2, OTP | Roles, authorities, permissions |
| Failure response | **401 Unauthorized** | **403 Forbidden** |

**Spring Security objects to name:**
- `Authentication`: holds the principal, credentials, and authorities.
- `SecurityContextHolder` / `SecurityContext`: stores the current user's `Authentication` (thread-local).
- `AuthenticationManager` → `AuthenticationProvider` (e.g. `DaoAuthenticationProvider`) → `UserDetailsService` + `PasswordEncoder` (use BCrypt).
- `GrantedAuthority`: a permission or role such as `ROLE_ADMIN`.

---

## 2. What is the Security Filter Chain?

Spring Security is built entirely on **servlet filters**. A request passes through an ordered chain of security filters before reaching the `DispatcherServlet` and your controller. Each filter has one job.

**How it plugs in:** the servlet container calls a single filter, `DelegatingFilterProxy` (Spring Boot registers it as the `springSecurityFilterChain` bean), which delegates to `FilterChainProxy`. That picks the matching `SecurityFilterChain` for the request and runs its filters.

---

## 3. What are the filter layers?

Common filters in order (simplified):

1. `SecurityContextHolderFilter`: loads or creates the `SecurityContext`.
2. `CorsFilter`
3. `CsrfFilter`
4. `LogoutFilter`
5. `UsernamePasswordAuthenticationFilter`: form login. (In JWT apps, **your custom JWT filter goes before this one**.)
6. `BasicAuthenticationFilter`
7. `AnonymousAuthenticationFilter`: assigns an anonymous user if nobody is authenticated.
8. `ExceptionTranslationFilter`: converts security exceptions into 401/403 via `AuthenticationEntryPoint` and `AccessDeniedHandler`.
9. `AuthorizationFilter` (previously `FilterSecurityInterceptor`): final check against your `authorizeHttpRequests` rules.

**Flow:** Request → filters (authenticate) → `AuthorizationFilter` (authorize) → controller. Two kinds of failure: `AuthenticationException` gives 401, and `AccessDeniedException` gives 403.

---

## 4. Security Filter Chain implementation and permitting only some endpoints

Spring Security 6 style, with no `WebSecurityConfigurerAdapter` (removed):

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity            // enables @PreAuthorize
@RequiredArgsConstructor
public class SecurityConfig {

    private final JwtAuthFilter jwtAuthFilter;

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())                         // fine for stateless token APIs
            .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**", "/swagger-ui/**", "/v3/api-docs/**").permitAll()
                .requestMatchers(HttpMethod.GET, "/api/products/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .requestMatchers("/api/orders/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated())                    // everything else needs login
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class)
            .exceptionHandling(e -> e
                .authenticationEntryPoint(...)                    // custom 401
                .accessDeniedHandler(...));                       // custom 403
        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() { return new BCryptPasswordEncoder(); }

    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration cfg) throws Exception {
        return cfg.getAuthenticationManager();
    }
}
```

**Points to mention:**
- **Rule order matters:** the first matching rule wins, so put specific rules first and `anyRequest()` last.
- `permitAll()` means no authorization needed. The request still passes through the filter chain. To skip the chain entirely, use `WebSecurityCustomizer` with `web.ignoring()`, which is only for static resources.
- `hasRole("ADMIN")` checks for authority `ROLE_ADMIN` (the prefix is added automatically). `hasAuthority("ROLE_ADMIN")` needs the full string.
- Disabling CSRF is acceptable for stateless APIs using bearer tokens. Keep it for cookie-based sessions.

---

## 5. What is a JWT and how does it work?

**JWT (JSON Web Token)** is a compact, **self-contained, signed** token used to prove identity without server-side sessions.

**Structure: `header.payload.signature`** (each Base64URL-encoded)
- **Header:** algorithm and type, e.g. `{"alg":"HS256","typ":"JWT"}`
- **Payload (claims):** `sub` (user), `roles`, `iat` (issued at), `exp` (expiry), `iss`, custom claims
- **Signature:** `HMAC_SHA256(base64(header) + "." + base64(payload), secret)`, or RSA/ECDSA with a private key

**Flow:**
1. The client sends credentials to `POST /login`.
2. The server authenticates them and **creates and signs** a JWT.
3. The client stores it and sends `Authorization: Bearer <token>` on every request.
4. A **JWT filter** verifies the signature and expiry, extracts the user and roles, and sets the `Authentication` in `SecurityContextHolder`.
5. `AuthorizationFilter` checks the roles, then the controller runs.

```java
@Component
public class JwtAuthFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest req, HttpServletResponse res, FilterChain chain)
            throws ServletException, IOException {
        String header = req.getHeader("Authorization");
        if (header != null && header.startsWith("Bearer ")) {
            String token = header.substring(7);
            if (jwtService.isValid(token)) {
                String username = jwtService.extractUsername(token);
                UserDetails user = userDetailsService.loadUserByUsername(username);
                var auth = new UsernamePasswordAuthenticationToken(user, null, user.getAuthorities());
                SecurityContextHolder.getContext().setAuthentication(auth);
            }
        }
        chain.doFilter(req, res);
    }
}
```

**Key points:**
- The payload is **encoded, not encrypted**, so anyone can read it. Never put passwords or sensitive data in it.
- The signature guarantees **integrity** (tampering is detected), not secrecy.
- **Stateless:** no session lookup, so it scales well across instances.
- Store the secret or private key securely (env/vault), and use a strong key (at least 256 bits for HS256).

---

## 6. If a user's role changes from USER to ADMIN, how does the JWT behave?

**The existing token keeps the old role until it expires**, because the roles were baked into the payload when it was signed and the server doesn't look them up again. The user stays `USER` for the token's lifetime, even though the DB says `ADMIN`. (The same applies in reverse: a demoted or disabled user keeps access until expiry, which is the more serious risk.)

**Ways to handle it:**
1. **Short-lived access tokens** (5 to 15 minutes) plus a **refresh token**. The new access token issued on refresh picks up the current role from the DB. This is the standard answer.
2. **Force re-login or revoke tokens** when roles change: keep a **blacklist/denylist** (Redis, with TTL equal to the token's remaining life) or a per-user `tokenVersion` / `roleChangedAt` that the filter compares against the token's claim.
3. **Don't trust roles in the token:** in the filter, load the user from the DB (or a cache) on each request, as `userDetailsService.loadUserByUsername` does above. This gives fresh authorities but costs a lookup, so cache it.
4. Opaque tokens with introspection (OAuth2), or sessions, if instant revocation is a hard requirement.

**Trade-off to state:** JWT is stateless, so instant revocation conflicts with statelessness. You choose between freshness and performance.

---

## 7. How do you validate whether a user has access to a particular module?

Layered approach:

**a) URL-level** (in the filter chain):
```java
.requestMatchers("/api/reports/**").hasAuthority("MODULE_REPORTS")
```

**b) Method-level** (`@EnableMethodSecurity`):
```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) { ... }

@PreAuthorize("hasAuthority('REPORT_VIEW')")
public Report getReport() { ... }

@PreAuthorize("#userId == authentication.principal.id or hasRole('ADMIN')")   // ownership check
public Profile getProfile(Long userId) { ... }
```
Also `@PostAuthorize` (checks the return value) and `@Secured`. Method security works through a **proxy**, so self-invocation is not covered.

**c) Data-driven module permissions (typical enterprise answer):**
- Model it as User → Role → Permission (module + action), e.g. `ORDERS_READ`, `ORDERS_WRITE`.
- Load these as `GrantedAuthority` objects at login, and put them in the JWT as a `permissions` claim, or fetch them from the DB or cache per request.
- Use `hasAuthority(...)`, or a custom bean: `@PreAuthorize("@accessService.canAccess(authentication, 'REPORTS')")`.

**Distinguish:** role-based (RBAC) vs permission-based vs attribute-based (ABAC). Mention that the **frontend hiding a button is not security**, and the backend must always enforce it.

---

## 8. What does a refresh token do?

A **refresh token** is a longer-lived credential used **only to obtain new access tokens**, so users don't need to log in again when the short-lived access token expires.

| | Access token | Refresh token |
|---|---|---|
| Lifetime | Short (5 to 15 min) | Long (days to weeks) |
| Sent to | Every API call | Only the `/refresh` endpoint |
| Purpose | Access resources | Get a new access token |
| Exposure risk | Higher (sent often), so keep it short | Lower, since it is rarely sent |

**Flow:**
1. Login returns an access token and a refresh token.
2. The access token expires, and the API returns 401.
3. The client calls `POST /auth/refresh` with the refresh token.
4. The server validates it (signature, expiry, not revoked, matches the DB), then issues a new access token. It often **rotates** the refresh token too.
5. If the refresh token has also expired, the user must log in again.

**Best practices:**
- **Store refresh tokens server-side** (DB/Redis) so they can be **revoked** (logout, password change, role change, theft).
- **Rotation:** issue a new refresh token on each use and invalidate the old one. If an old one is reused, treat it as theft and revoke the whole token family.
- Store in an **HttpOnly, Secure, SameSite cookie** (not localStorage) on the web.
- Re-read the user's current roles from the DB when issuing the new access token, which solves the role-change problem from Q6.

---

## 9. How do you set the JWT expiration?

Set the `exp` claim when building the token. With **jjwt**:

```java
@Value("${jwt.secret}") private String secret;
@Value("${jwt.expiration-ms}") private long expirationMs;   // e.g. 900000 = 15 min

public String generateToken(UserDetails user) {
    Date now = new Date();
    return Jwts.builder()
            .subject(user.getUsername())
            .claim("roles", user.getAuthorities().stream().map(GrantedAuthority::getAuthority).toList())
            .issuedAt(now)
            .expiration(new Date(now.getTime() + expirationMs))   // exp claim
            .signWith(Keys.hmacShaKeyFor(secret.getBytes(StandardCharsets.UTF_8)))
            .compact();
}
```

```properties
jwt.secret=${JWT_SECRET}
jwt.expiration-ms=900000
jwt.refresh-expiration-ms=604800000
```

**Validation:** when parsing, the library **automatically throws `ExpiredJwtException`** if `exp` has passed. Catch it in the filter and return 401 (or let the client call `/refresh`).

```java
try {
    Jwts.parser().verifyWith(key).build().parseSignedClaims(token);
} catch (ExpiredJwtException e) { /* 401 token expired */ }
  catch (JwtException e)        { /* 401 invalid token */ }
```

**Note:** the API differs between jjwt versions (older versions use `setSubject`, `setExpiration`, `signWith(key, SignatureAlgorithm.HS256)`), so match the syntax to the version in your project.

---

## Likely follow-ups (Security)

- **Where do you store the JWT on the client?** HttpOnly cookie (safer against XSS, needs CSRF protection) vs localStorage (easy, but vulnerable to XSS).
- **HS256 vs RS256?** HS256 uses one shared secret (simple, single service). RS256 uses a private key to sign and a public key to verify, which suits microservices because others can verify without being able to issue tokens.
- **How do you log out with JWT?** Delete the client token, revoke the refresh token, and denylist the access token until it expires.
- **401 vs 403?** Not authenticated vs authenticated but not permitted.
- **Session-based vs token-based auth?** Sessions are stateful (server memory or Redis, cookie `JSESSIONID`). Tokens are stateless and scale easily, but revocation is harder.
- **What is OAuth2?** An authorization framework (Authorization Code flow with PKCE, client credentials, etc.). OpenID Connect adds authentication on top.
- **Why `OncePerRequestFilter`?** It guarantees a single execution per request, even with forwards and error dispatches.
- **How are passwords stored?** Hashed with a salt using BCrypt or Argon2, never plain text or reversible encryption.

---

# Batch 8: Microservices

## 1. How do services communicate with each other?

Two broad styles:

**Synchronous (request/response):** the caller waits for the reply.
- **REST over HTTP** using `RestTemplate`, `RestClient`, `WebClient`, or declarative clients like **OpenFeign** / Spring's `@HttpExchange` interfaces.
- **gRPC:** binary (Protobuf) over HTTP/2, faster and strongly typed, good for internal service-to-service calls.
- **GraphQL**, less common between services.

**Asynchronous (event/message based):** the caller sends a message and moves on.
- **Message brokers:** Kafka, RabbitMQ, ActiveMQ, AWS SQS/SNS.
- Patterns: publish/subscribe, event-driven architecture, event sourcing.

**Supporting pieces:**
- **Service discovery** (Eureka, Consul, Kubernetes DNS) so services find each other by name, not hard-coded IP.
- **Client-side load balancing** (Spring Cloud LoadBalancer).
- **Resilience** (timeouts, retries, circuit breaker), covered below.
- **Auth between services:** JWT/OAuth2 propagation, or mTLS.

---

## 2. What is an API Gateway?

A **single entry point** for all client requests. It sits in front of the microservices and routes each request to the right one.

**Responsibilities:**
- **Routing** (`/orders/**` goes to order-service)
- **Authentication/authorization** (validate the JWT once at the edge)
- **Rate limiting and throttling**
- **Load balancing** and service discovery integration
- **Cross-cutting concerns:** logging, tracing, CORS, SSL termination, caching
- **Request/response transformation** and **aggregation** (combine several service calls into one response)
- Circuit breaker and retry at the edge

**Tools:** Spring Cloud Gateway (reactive, built on WebFlux), Netflix Zuul (legacy), Kong, NGINX, AWS API Gateway.

```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: order-service
          uri: lb://ORDER-SERVICE
          predicates:
            - Path=/api/orders/**
          filters:
            - StripPrefix=1
```

**Trade-off:** it can become a bottleneck or single point of failure if not made highly available, and it shouldn't hold business logic.

**Related:** the **BFF (Backend for Frontend)** pattern uses one gateway per client type (web, mobile).

---

## 3. Features of microservices

- **Single responsibility:** each service owns one business capability.
- **Independent deployment:** deploy, scale, and release one service without touching others.
- **Decentralized data:** **database per service**, so no shared schema.
- **Loose coupling** via APIs or events.
- **Technology diversity:** each team can pick its own stack.
- **Independent scaling:** scale only the hot services.
- **Fault isolation:** one failure doesn't take down everything (with proper resilience patterns).
- **Small, autonomous teams** (Conway's law).
- **Automation:** CI/CD, containers (Docker), orchestration (Kubernetes).
- **Observability:** centralized logging (ELK), distributed tracing (Micrometer Tracing/Zipkin), metrics (Prometheus/Grafana).
- **Supporting infrastructure:** API gateway, service discovery, config server.

**Challenges (interviewers often ask):** distributed transactions, data consistency, network latency, debugging, operational complexity, and versioning.

---

## 4. How are transactions handled in microservices?

A normal `@Transactional` only covers **one database**. With a database per service, you can't use a single ACID transaction across services, so you use **eventual consistency** patterns.

### Saga pattern (the standard answer)
A saga is a sequence of **local transactions**, one per service. If a step fails, **compensating transactions** undo the earlier steps.

Example: Order, Payment, Inventory.
1. Order service creates the order (`PENDING`).
2. Payment service charges the card.
3. Inventory service reserves stock. **It fails.**
4. Compensation: refund the payment, then cancel the order.

Two styles:
- **Choreography:** services react to each other's events via Kafka. No central coordinator, which is simple but harder to follow as it grows.
- **Orchestration:** a central orchestrator (e.g. Camunda, Temporal, or your own service) tells each service what to do and triggers compensations. Easier to reason about and monitor.

### Supporting patterns
- **Outbox pattern:** write the business data and the event to an `outbox` table in the **same local DB transaction**, and a separate process (poller or CDC, e.g. Debezium) publishes it to Kafka. This avoids the "DB commit succeeded but message failed" problem (the dual-write problem).
- **Idempotent consumers:** messages can be delivered more than once, so processing must be safe to repeat (idempotency keys, dedup table).
- **Retries** with backoff and **dead-letter queues**.
- **Event sourcing and CQRS**, sometimes.

### Alternative: 2PC
Avoided in most microservices (see Q6).

**Key statement:** you trade strong consistency for availability and scalability, per the CAP theorem. The system is **eventually consistent**.

---

## 5. What is the Circuit Breaker pattern?

It prevents a service from repeatedly calling a **failing or slow downstream service**, which would exhaust threads and cause **cascading failures**. It works like an electrical circuit breaker.

**Three states:**
1. **CLOSED:** calls flow normally. Failures are counted.
2. **OPEN:** once the failure rate or slow-call rate crosses a threshold, the circuit trips. Calls **fail immediately** (or go to a fallback) without hitting the downstream service.
3. **HALF-OPEN:** after a wait time, a few trial calls are allowed. If they succeed, it goes back to CLOSED. If they fail, it goes back to OPEN.

```java
@CircuitBreaker(name = "paymentService", fallbackMethod = "paymentFallback")
public PaymentResponse pay(PaymentRequest req) {
    return paymentClient.charge(req);
}

public PaymentResponse paymentFallback(PaymentRequest req, Throwable t) {
    return PaymentResponse.pending("Payment service unavailable, will retry");
}
```

```yaml
resilience4j.circuitbreaker.instances.paymentService:
  slidingWindowSize: 10
  failureRateThreshold: 50
  waitDurationInOpenState: 10s
  permittedNumberOfCallsInHalfOpenState: 3
```

**Library:** **Resilience4j** is the standard now. Hystrix is deprecated.

**Related resilience patterns:** **Retry** (with exponential backoff and jitter), **Timeout**, **Bulkhead** (isolate resources per dependency), **Rate limiter**, **Fallback**.

---

## 6. What is a two-phase commit (2PC)?

A **distributed transaction protocol** that makes multiple resources (databases, queues) commit or roll back **atomically**, coordinated by a **transaction coordinator**.

**Phase 1: Prepare (voting):**
- The coordinator asks every participant: "Can you commit?"
- Each participant does the work, **locks resources**, writes to its log, and replies YES or NO.

**Phase 2: Commit/Rollback:**
- If **all** said YES, the coordinator sends **COMMIT** to everyone.
- If **any** said NO (or timed out), it sends **ROLLBACK** to everyone.

**In Java:** JTA/XA transactions (Atomikos, Narayana, Bitronix), with XA-capable resources.

**Why it is avoided in microservices:**
- **Blocking:** locks are held while waiting, which hurts throughput.
- **Coordinator is a single point of failure.** If it crashes after prepare, participants stay blocked ("in doubt").
- **Poor scalability** and high latency across the network.
- Requires all resources to support XA, and many NoSQL stores and brokers like Kafka don't.
- **Tight coupling** between services, which defeats the point of microservices.

**Alternative:** Saga plus outbox (eventual consistency). **Three-phase commit (3PC)** adds a pre-commit phase to reduce blocking but is rarely used in practice.

**When 2PC is still fine:** a monolith or tightly controlled setting with a few XA databases.

---

## 7. When to use synchronous vs asynchronous communication?

| | Synchronous | Asynchronous |
|---|---|---|
| Use when | The caller **needs the response now** to continue | The caller **doesn't need an immediate result** |
| Examples | Fetch user profile, validate a card, check price, login | Send email, generate report, order processing, notifications, audit logs, analytics |
| Coupling | Tighter (temporal coupling, so the callee must be up) | Loose, since the broker buffers messages |
| Failure handling | Needs timeouts, retries, circuit breaker | Retries, DLQ, the consumer can catch up later |
| Latency | Adds to the user-facing response time | Faster response to the user |
| Complexity | Simple, easy to debug | Harder: eventual consistency, ordering, duplicates, tracing |
| Scalability | Limited by the slowest service | Handles spikes by queueing |

**Rule of thumb:**
- **Queries and real-time answers:** synchronous.
- **Commands, workflows, and long-running or fan-out work:** asynchronous (events).
- **Cross-service data changes:** prefer async events (saga) over chains of sync calls.

**Avoid:** long chains of synchronous calls (A calls B calls C calls D). The availability is the product of all services, and latency adds up. You can mix both: respond with `202 Accepted` and process asynchronously.

---

## 8. What is WebClient?

A **non-blocking, reactive HTTP client** from **Spring WebFlux** (`spring-boot-starter-webflux`). It returns `Mono<T>` (0 or 1 item) or `Flux<T>` (0 to N items) and is built on Reactor Netty.

```java
WebClient client = WebClient.builder().baseUrl("http://order-service").build();

Mono<Order> order = client.get()
        .uri("/orders/{id}", 42)
        .retrieve()
        .onStatus(HttpStatusCode::is4xxClientError, r -> Mono.error(new NotFoundException()))
        .bodyToMono(Order.class)
        .timeout(Duration.ofSeconds(3));

Order result = order.block();   // only if you really need to block
```

**Features:** non-blocking I/O (few threads handle many concurrent calls), streaming, a fluent API, filters (`ExchangeFilterFunction`) for auth and logging, and it works for both sync (`.block()`) and async use.

**Use when:** you're on a reactive stack, need high concurrency, call many services in parallel, or need streaming.

**Caution:** calling `.block()` inside a reactive pipeline is an anti-pattern. If your app is a normal servlet MVC app, `WebClient` brings the reactive learning curve.

---

## 9. What is RestClient?

Introduced in **Spring Framework 6.1 / Spring Boot 3.2**. A **modern, synchronous (blocking), fluent HTTP client** that is the recommended replacement for `RestTemplate` in blocking apps. It has the same fluent style as `WebClient` but without Reactor.

```java
RestClient client = RestClient.builder().baseUrl("http://order-service").build();

Order order = client.get()
        .uri("/orders/{id}", 42)
        .retrieve()
        .body(Order.class);

Order created = client.post()
        .uri("/orders")
        .contentType(MediaType.APPLICATION_JSON)
        .body(newOrder)
        .retrieve()
        .onStatus(HttpStatusCode::is4xxClientError, (req, res) -> { throw new BadRequestException(); })
        .body(Order.class);
```

**Why it exists:** `RestTemplate` has a bloated API with many overloads, while `RestClient` is cleaner and shares infrastructure (message converters, request factories) with it. It also supports `@HttpExchange` declarative interfaces.

**Use when:** a Spring MVC (non-reactive) app on Spring Boot 3.2+ needs a blocking HTTP client.

---

## 10. What is RestTemplate?

The **classic synchronous (blocking) HTTP client** in Spring, in use since Spring 3.

```java
@Bean
public RestTemplate restTemplate(RestTemplateBuilder builder) {
    return builder.connectTimeout(Duration.ofSeconds(2))
                  .readTimeout(Duration.ofSeconds(5))
                  .build();
}

User user = restTemplate.getForObject("http://user-service/users/{id}", User.class, 1);

ResponseEntity<User> resp = restTemplate.postForEntity(url, request, User.class);

restTemplate.exchange(url, HttpMethod.PUT, new HttpEntity<>(body, headers), Void.class);
```

**Common methods:** `getForObject`, `getForEntity`, `postForObject`, `postForEntity`, `put`, `delete`, `exchange` (full control), `execute`.

**Status:** it is in **maintenance mode**, so there are no new features and `RestClient` is the recommended replacement. It's still widely used in existing code.

**With service discovery:** annotate the bean with `@LoadBalanced` to call `http://user-service/...` by name.

**Gotchas:** it blocks a thread per call, so always set connect and read **timeouts** (the defaults can be infinite). Reuse a single bean instead of creating one per request.

### Quick comparison

| | RestTemplate | RestClient | WebClient |
|---|---|---|---|
| Style | Blocking | Blocking | Non-blocking (reactive) |
| API | Old, many overloads | Modern fluent | Modern fluent |
| Since | Spring 3 | Spring 6.1 / Boot 3.2 | Spring 5 |
| Status | Maintenance mode | Recommended for sync | Recommended for reactive |
| Dependency | `spring-web` | `spring-web` | `spring-webflux` |

---

## Likely follow-ups (Microservices)

- **OpenFeign vs RestTemplate?** Feign is declarative (an interface with annotations), integrates with load balancing and Resilience4j, and has less boilerplate.
- **Service discovery: client-side vs server-side?** Eureka, with the client picking an instance, vs Kubernetes Service, where the platform load-balances.
- **What is a distributed tracing ID?** A trace ID propagated across services (Micrometer Tracing + Zipkin/OpenTelemetry) to follow one request end to end.
- **CAP theorem?** A distributed system can guarantee only two of Consistency, Availability, and Partition tolerance. Since partitions are unavoidable, you choose between C and A.
- **How do you handle versioning of APIs?** URI (`/v1/`), header, or media type versioning, with backward compatibility.
- **Monolith vs microservices?** Start with a monolith for small teams. Move to microservices when scaling teams or components independently justifies the operational cost.
- **Config management?** Spring Cloud Config, Kubernetes ConfigMaps/Secrets, HashiCorp Vault.

---

# Batch 9: AWS

## 1. How do you push a file to an S3 bucket using Spring Boot?

Use the **AWS SDK for Java v2** (`software.amazon.awssdk:s3`), or **Spring Cloud AWS** for less boilerplate.

**Dependency (Maven):**
```xml
<dependency>
    <groupId>software.amazon.awssdk</groupId>
    <artifactId>s3</artifactId>
</dependency>
```
(Manage the version through the AWS SDK BOM.)

**Config:**
```java
@Configuration
public class S3Config {
    @Bean
    public S3Client s3Client(@Value("${aws.region}") String region) {
        return S3Client.builder()
                .region(Region.of(region))
                .credentialsProvider(DefaultCredentialsProvider.create())  // env, profile, or IAM role
                .build();
    }
}
```

**Service:**
```java
@Service
@RequiredArgsConstructor
public class S3Service {
    private final S3Client s3;

    @Value("${aws.s3.bucket}")
    private String bucket;

    public String upload(MultipartFile file) throws IOException {
        String key = "uploads/" + UUID.randomUUID() + "-" + file.getOriginalFilename();

        PutObjectRequest request = PutObjectRequest.builder()
                .bucket(bucket)
                .key(key)
                .contentType(file.getContentType())
                .build();

        s3.putObject(request, RequestBody.fromInputStream(file.getInputStream(), file.getSize()));
        return key;
    }
}
```

**Controller:**
```java
@PostMapping("/files")
public ResponseEntity<String> upload(@RequestParam("file") MultipartFile file) throws IOException {
    return ResponseEntity.status(HttpStatus.CREATED).body(s3Service.upload(file));
}
```

```properties
spring.servlet.multipart.max-file-size=50MB
spring.servlet.multipart.max-request-size=50MB
aws.region=ap-south-1
aws.s3.bucket=my-bucket
```

**Points that impress:**
- **Never hard-code access keys.** Use the **default credentials chain**: env variables, `~/.aws/credentials`, or best of all an **IAM role** attached to the EC2/ECS/EKS/Lambda runtime. Grant least-privilege permissions (`s3:PutObject` on that bucket only).
- **Large files:** use **multipart upload** or the SDK's `S3TransferManager` (parallel and resumable) instead of loading everything into memory. Prefer streaming (`RequestBody.fromInputStream`) over `file.getBytes()`.
- **Pre-signed URLs:** let the client upload **directly to S3** (`S3Presigner`), which keeps large traffic off your server.
- **Validation:** check file type and size, sanitize the file name, and use generated keys.
- **Security and cost:** enable server-side encryption (SSE-S3/SSE-KMS), keep the bucket private, use versioning and lifecycle rules.
- Spring Cloud AWS alternative: `S3Template.upload(bucket, key, inputStream)`.

---

## 2. Lambda, EKS, ECS and related services

### Compute

| Service | What it is | Use when |
|---|---|---|
| **EC2** | Virtual machines you manage fully | Full control, legacy apps, custom OS |
| **Lambda** | **Serverless functions**: upload code, runs on events, pay per invocation and duration, auto-scales, no servers to manage. Max 15 min per run | Event-driven tasks (S3 upload trigger, API Gateway endpoints, cron jobs, queue consumers) |
| **ECS** | AWS's own **container orchestration** service. Runs Docker containers as tasks and services | Simple container deployment, AWS-native, less complexity than Kubernetes |
| **EKS** | **Managed Kubernetes**. AWS runs the control plane, and you run worker nodes | You need Kubernetes (portability, ecosystem, multi-cloud, existing K8s skills) |
| **Fargate** | **Serverless compute engine for containers**. Works with ECS and EKS, with no EC2 nodes to manage | Run containers without managing servers |
| **Elastic Beanstalk** | PaaS: upload a JAR and it handles provisioning, load balancing, scaling | Quick deployment of simple apps |
| **ECR** | Private **Docker image registry** | Store container images |

**ECS vs EKS:** ECS is simpler and AWS-specific. EKS is Kubernetes, more powerful and portable but more complex.

**Lambda notes:** watch for **cold starts** (Java is slower here, mitigated with SnapStart, GraalVM native image, or provisioned concurrency), and it is stateless.

### Other commonly asked services

- **S3:** object storage.
- **RDS / Aurora:** managed relational DB. **DynamoDB:** managed NoSQL.
- **SQS:** message queue. **SNS:** pub/sub notifications. **MSK:** managed Kafka. **EventBridge:** event bus.
- **API Gateway:** managed API front door (routing, auth, throttling).
- **ElastiCache:** managed Redis/Memcached.
- **ALB / NLB:** load balancers. **Route 53:** DNS. **CloudFront:** CDN.
- **VPC, subnets, security groups:** networking and firewall rules.
- **IAM:** users, roles, policies (access control).
- **CloudWatch:** logs, metrics, alarms. **X-Ray:** tracing. **CloudTrail:** audit of API calls.
- **Secrets Manager / Parameter Store:** store credentials and config. **KMS:** encryption keys.
- **CloudFormation / Terraform / CDK:** infrastructure as code.
- **Auto Scaling Group:** scale EC2 instances automatically.

---

## 3. How do you create and deploy a build in AWS?

A typical **CI/CD pipeline**:

```
Code push (Git) → Build & test → Package (JAR / Docker image) → Store artifact → Deploy → Monitor
```

### Option A: AWS-native pipeline
- **CodeCommit/GitHub**: source.
- **CodeBuild**: runs `mvn clean package` and tests, and builds a Docker image, using a `buildspec.yml`.
- **CodePipeline**: orchestrates the stages.
- **CodeDeploy**: deploys to EC2, ECS, or Lambda (rolling, blue/green).

```yaml
# buildspec.yml
version: 0.2
phases:
  install:
    runtime-versions:
      java: corretto17
  pre_build:
    commands:
      - aws ecr get-login-password | docker login --username AWS --password-stdin $ECR_URI
  build:
    commands:
      - mvn clean package -DskipTests=false
      - docker build -t $ECR_URI/myapp:$CODEBUILD_RESOLVED_SOURCE_VERSION .
      - docker push $ECR_URI/myapp:$CODEBUILD_RESOLVED_SOURCE_VERSION
artifacts:
  files:
    - target/*.jar
```

### Option B: Jenkins / GitHub Actions / GitLab CI
Same flow, with the CI tool building and pushing to AWS. This is very common in companies.

### Containerize the app
```dockerfile
FROM eclipse-temurin:17-jre
COPY target/app.jar app.jar
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

### Deployment targets
| Target | How |
|---|---|
| **EC2** | Copy the JAR (via S3 and CodeDeploy), run it as a systemd service behind an ALB |
| **Elastic Beanstalk** | Upload the JAR, and it provisions everything |
| **ECS (Fargate)** | Push the image to ECR, update the **task definition**, and the ECS service rolls out new tasks |
| **EKS** | Push to ECR, then `kubectl apply` / Helm, with a Deployment rolling update |
| **Lambda** | Package a JAR/zip or container image, and deploy with SAM, Serverless Framework, or CDK |

### Deployment strategies
- **Rolling:** replace instances gradually.
- **Blue/Green:** run a new environment alongside the old one and switch traffic, with instant rollback.
- **Canary:** send a small percentage of traffic to the new version first.

### Good practices to mention
- **Config and secrets** outside the code: environment variables, Parameter Store, Secrets Manager, with Spring profiles per environment (dev, qa, prod).
- **IAM roles** for the task or instance, not keys.
- **Health checks:** Spring Boot Actuator (`/actuator/health`) for the load balancer and ECS/K8s.
- **Observability:** CloudWatch logs and metrics, alarms, and tracing.
- **Infrastructure as code** (Terraform/CloudFormation) so environments are reproducible.
- Pipeline stages: unit tests, static analysis (SonarQube), security scan, deploy to staging, approval, deploy to prod, then automated **rollback** on failure.

**A short spoken answer:** "I push code to Git, which triggers a pipeline (Jenkins or CodePipeline). It runs Maven build and tests, builds a Docker image, and pushes it to ECR. Then it deploys to ECS Fargate or EKS using a rolling or blue/green strategy, with config from Parameter Store, an IAM role for permissions, and CloudWatch for monitoring and alarms."

---

## Likely follow-ups (AWS)

- **Lambda vs EC2 vs containers?** Lambda for short, event-driven, spiky workloads. Containers for long-running services. EC2 for full control.
- **How does S3 trigger a Lambda?** Configure an S3 event notification (e.g. `ObjectCreated`) pointing to the function.
- **S3 consistency model?** S3 now provides **strong read-after-write consistency**.
- **S3 storage classes?** Standard, Intelligent-Tiering, Standard-IA, Glacier tiers, for cost optimization.
- **Security groups vs NACLs?** Security groups are stateful and instance-level. NACLs are stateless and subnet-level.
- **How do you handle a rollback?** Redeploy the previous image tag/task definition, switch traffic back in blue/green, or use CodeDeploy automatic rollback on alarm.

---

# Batch 10: Scenario-Based Questions

## 1. API slowed from 5s to 20s because of large countries. How do you optimize at the API level?

I'm reading "big countries" as **countries with a very large volume of data** (many records, cities, or users per country). That makes the API do far more work for those requests. Without touching the DB or infrastructure, I'd attack it in this order.

**Step 0: Measure first.** Never optimize blindly. Use logs with timings, Micrometer/Actuator metrics, a profiler (VisualVM, JFR), or APM (New Relic, Datadog). Find out where the 20s goes: fetching data, processing in Java, mapping, serialization, or calls to other services.

**Optimizations:**

| Technique | What to do |
|---|---|
| **Pagination** | Never return everything. Use `Pageable` (`page`, `size`) or **keyset/cursor pagination** for huge sets. Cap the maximum page size. |
| **Return less data** | Use DTOs/projections with only the needed fields. Avoid sending whole entity graphs. Support field filtering. |
| **Caching** | Cache results for large, rarely changing countries (see Q3). Cache expensive computed results too. |
| **Parallel processing** | Break the work into chunks (by state or region) and run it with `CompletableFuture` / a custom thread pool, then merge. Use `parallelStream` only for CPU-bound work, and carefully. |
| **Async processing** | If the result isn't needed immediately: return `202 Accepted` with a job ID, process in the background, and let the client poll or get a callback. Or generate a report and give a download link. |
| **Fix inefficient code** | Remove nested loops (O(n²)), repeated lookups inside loops (use a `HashMap`/`Set` built once), and **N+1 calls to other services or repositories inside loops** (batch them). |
| **Efficient data structures and streaming** | Avoid loading millions of objects into memory. Process in chunks or stream. Avoid repeated string concatenation and unnecessary object creation. |
| **Response compression** | `server.compression.enabled=true` (gzip), which helps a lot for large JSON. |
| **Faster serialization** | Avoid serializing huge object graphs. Stream the JSON for very large responses (`StreamingResponseBody`, Jackson streaming). |
| **Timeouts and resilience** | Set timeouts on downstream calls, plus circuit breaker and fallback so one slow dependency doesn't stall the whole request. |
| **Pre-computation** | Precompute heavy aggregates with a scheduled job and serve the stored result, rather than computing per request. |
| **HTTP caching** | `ETag` / `Cache-Control` so clients don't re-fetch unchanged data. |
| **Rate limiting or query limits** | Reject or throttle abusive "fetch everything" requests. |

**Short spoken answer:** "First I'd profile to find the bottleneck. Since it's slow only for countries with large data volume, I'd add pagination and return only the needed fields via DTOs, cache the results for heavy countries, remove any loops or per-item calls and batch them, and parallelize independent parts with CompletableFuture. If it's still heavy, I'd make it asynchronous with a job ID. Then I'd compare timings before and after to confirm the improvement."

**Mention the principle:** the slowdown scales with data size, so the fix is to *bound* the work per request (pagination), *avoid repeating* work (caching), and *do independent work in parallel*.

---

## 2. An endpoint is for admins only, but a normal user calls it. How do you handle it?

The user is **authenticated but not authorized**, so the server must respond with **403 Forbidden** (not 401, and not a 500).

**Enforce at multiple levels:**

**a) URL level:**
```java
.authorizeHttpRequests(auth -> auth
    .requestMatchers("/api/admin/**").hasRole("ADMIN")
    .anyRequest().authenticated())
```

**b) Method level:**
```java
@PreAuthorize("hasRole('ADMIN')")
@DeleteMapping("/users/{id}")
public void deleteUser(@PathVariable Long id) { ... }
```
(Requires `@EnableMethodSecurity`.)

**c) Custom 403 response:**
```java
@Component
public class CustomAccessDeniedHandler implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest req, HttpServletResponse res, AccessDeniedException ex)
            throws IOException {
        res.setStatus(HttpServletResponse.SC_FORBIDDEN);
        res.setContentType("application/json");
        res.getWriter().write("{\"status\":403,\"message\":\"You do not have permission to access this resource\"}");
    }
}
```
Register it with `.exceptionHandling(e -> e.accessDeniedHandler(handler))`.

Note: `@PreAuthorize` failures happen inside the controller layer, so an `@ExceptionHandler(AccessDeniedException.class)` in your `@RestControllerAdvice` also works for method security.

**Additional points:**
- **Log the attempt** for auditing and security alerts (repeated attempts may indicate probing).
- **Return no sensitive details** in the error message.
- **Frontend hiding the button is not security.** The backend must always enforce it.
- Some teams return **404 instead of 403** for sensitive resources, to avoid revealing that the endpoint exists. Mention it as a design choice.
- If the user *should* sometimes get access (for example a one-time elevated action), use a proper access-request workflow or fine-grained permissions rather than weakening the check.

---

## 3. How do you implement caching in a Spring Boot application?

Use **Spring Cache abstraction** with a provider.

**Step 1: dependency and enable:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```
```java
@SpringBootApplication
@EnableCaching
public class App { }
```

**Step 2: annotations:**
```java
@Service
public class CountryService {

    @Cacheable(value = "countries", key = "#code")
    public CountryDto getCountry(String code) {
        return repo.findByCode(code)...;    // runs only on a cache miss
    }

    @CachePut(value = "countries", key = "#dto.code")
    public CountryDto update(CountryDto dto) { ... }     // updates the cache, always runs

    @CacheEvict(value = "countries", key = "#code")
    public void delete(String code) { ... }              // removes the entry

    @CacheEvict(value = "countries", allEntries = true)
    public void refreshAll() { ... }
}
```

| Annotation | Behavior |
|---|---|
| `@Cacheable` | Checks the cache first. If it's a hit, the method is skipped. Otherwise it runs and stores the result. |
| `@CachePut` | Always runs the method and updates the cache. |
| `@CacheEvict` | Removes an entry (or all entries). |
| `@Caching` / `@CacheConfig` | Combine annotations / share class-level settings. |

Options: `condition`, `unless` (e.g. `unless = "#result == null"`), `sync = true` (prevents a cache stampede).

**Step 3: pick a cache provider:**

| Provider | Use |
|---|---|
| **ConcurrentMapCache** (default) | Simple, in-memory, no TTL. Dev/testing only. |
| **Caffeine** | Fast **local (in-JVM)** cache with TTL, max size, eviction. Great for a single instance. |
| **Redis** | **Distributed** cache shared across all instances. Best for microservices and multiple pods. |
| Ehcache, Hazelcast | Other options. |

**Caffeine config:**
```java
@Bean
public CacheManager cacheManager() {
    CaffeineCacheManager manager = new CaffeineCacheManager("countries");
    manager.setCaffeine(Caffeine.newBuilder()
            .expireAfterWrite(10, TimeUnit.MINUTES)
            .maximumSize(1000));
    return manager;
}
```

**Redis config:**
```properties
spring.cache.type=redis
spring.data.redis.host=localhost
spring.data.redis.port=6379
spring.cache.redis.time-to-live=600000
```
Use JSON serialization (`GenericJackson2JsonRedisSerializer`) and make cached objects `Serializable` or serializable by the chosen serializer.

**Pitfalls interviewers ask about:**
- **Proxy-based, so self-invocation bypasses the cache**, just like `@Transactional`. Methods must be `public`.
- **Stale data:** set a **TTL** and evict or update on writes. Choose the key carefully.
- **Cache stampede** (many requests miss at once): use `sync = true` or locking.
- **Cache penetration** (repeated lookups for nonexistent keys): cache the null/empty result for a short time.
- **Don't cache** user-specific sensitive data without a user-aware key, or data that changes constantly.
- **Local vs distributed:** a local cache gives each instance its own copy, so you can get inconsistent data across instances. Redis solves that at the cost of a network hop.
- **Memory:** always set a max size and an eviction policy.

---

## 4. What was your role and responsibilities in your previous project?

This is a **template you can adapt with your real details**. Interviewers want specifics, not generic lines. Prepare these five parts:

**1. Project context (1 to 2 sentences):**
"I worked on a [domain, e.g. banking/e-commerce/insurance] platform built with **Java 17, Spring Boot, microservices, Kafka, PostgreSQL, and AWS**, serving [scale, e.g. X thousand users or Y transactions per day]."

**2. Your role and team:**
"I was a **Senior Java Developer** in a team of [N], working with [QA, DevOps, product owner] in Agile/Scrum."

**3. Responsibilities (pick the ones that are true for you):**
- Designed and developed **REST APIs** and microservices using Spring Boot.
- Wrote business logic, JPA/Hibernate data access, and managed **transactions**.
- Implemented **security** (Spring Security + JWT, role-based access).
- Integrated with other services via **Kafka/REST (Feign/WebClient)**, with resilience patterns like circuit breaker and retry.
- Handled **exception handling, validation, logging**.
- Wrote **unit and integration tests** (JUnit, Mockito, Testcontainers).
- Did **performance tuning** (caching, pagination, query optimization).
- Worked on **CI/CD** (Jenkins/GitHub Actions, Docker), deployments to **AWS**.
- Participated in **code reviews**, mentored juniors, and took part in design discussions.
- Production support: debugging issues, analyzing logs, and fixing defects.

**4. Achievements with numbers (this is what stands out):**
- "Reduced API response time from 8s to 2s by adding pagination and Redis caching."
- "Migrated a module from monolith to microservices."
- "Raised test coverage from 50% to 80%."
- "Fixed a production deadlock / memory leak."

**5. Challenges and what you learned:**
Prepare one real story: the problem, what you did, and the result.

**Tips:**
- Keep it to **1.5 to 2 minutes**, and finish by connecting to the role you're applying for.
- Use "I" for your own contributions and "we" for team work. Be honest, because interviewers will dig into whatever you claim (they might ask you to explain a Kafka or transaction decision you mention).
- Link your answers to the topics you've been preparing, since follow-ups will come from those.

---

## Likely follow-ups (Scenarios)

- **Which problem did you optimize and by how much?** Have one concrete example with numbers.
- **Offset vs keyset pagination?** Offset gets slower as the page number grows. Keyset (`WHERE id > last_id LIMIT n`) stays fast.
- **How do you decide what to cache?** Read-heavy, expensive, and rarely changing data.
- **Cache invalidation strategies?** TTL, write-through, write-behind, evict-on-update.
- **401 vs 403?** Not authenticated vs authenticated but not permitted.
- **How do you debug a slow API in production?** Metrics and traces, logs with timings, thread dumps, profiler, then isolate the slow component.

---

*Good luck with your interview!*
