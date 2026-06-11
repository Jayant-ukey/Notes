# Que - What is a race condition?

### ✅ **What is a Race Condition? (Interview-level answer – 5 YOE)**

A **race condition** occurs in a multithreaded environment when **two or more threads access and modify shared data concurrently**, and the **final result depends on the unpredictable order of execution**.

👉 Because thread scheduling is non-deterministic, the output becomes **inconsistent or incorrect**.

---

## 🔍 **Simple definition (what interviewer expects)**

“A race condition is a situation where multiple threads access shared resources without proper synchronization, leading to inconsistent or unexpected results.”

---

## ⚙️ **Example scenario**

Let’s say two threads increment a shared variable:

```java id="r1a9kp"
class Counter {
    int count = 0;

    public void increment() {
        count++; // not atomic operation
    }
}
```

### Two threads execute:

* Thread-1 → reads count = 0 → increments → writes 1
* Thread-2 → reads count = 0 → increments → writes 1

👉 Expected result: 2
👉 Actual result: 1 ❌

---

## 🧠 **Why this happens**

Because `count++` is NOT atomic. It consists of 3 steps:

1. Read value
2. Increment value
3. Write back

So multiple threads can interfere in between these steps.

---

## ⚠️ **Key characteristics of race condition**

* Happens only in **concurrent execution**
* Involves **shared mutable state**
* Depends on **thread scheduling order**
* Leads to **data inconsistency**

---

## 🔥 **Where it commonly occurs**

In real-world Java/Spring Boot systems:

* Shared counters or caches
* Updating shared collections without synchronization
* Banking transactions (balance update)
* Multi-threaded request handling
* Shared in-memory state in services

---

## 🛠️ **How to prevent race condition (important follow-up)**

### 1. Synchronization

```java id="s2k8lm"
synchronized void increment() {
    count++;
}
```

---

### 2. Atomic classes

```java id="t9v3qp"
AtomicInteger count = new AtomicInteger();

count.incrementAndGet();
```

---

### 3. Locks (ReentrantLock)

Used for more controlled synchronization.

---

### 4. Thread-safe collections

* `ConcurrentHashMap`
* `CopyOnWriteArrayList`

---

### 5. Immutability (best design practice)

Avoid shared mutable state wherever possible.

---

## 🧠 **Difference from deadlock (common follow-up confusion)**

| Concept | Race Condition          | Deadlock                 |
| ------- | ----------------------- | ------------------------ |
| Problem | Incorrect result        | System freeze            |
| Cause   | Lack of synchronization | Circular lock dependency |
| Outcome | Data inconsistency      | Threads stuck forever    |

---

## ⭐ **Strong closing statement**

“A race condition occurs when multiple threads access and modify shared data concurrently without proper synchronization, leading to inconsistent results due to unpredictable execution order. It can be prevented using synchronization, atomic variables, locks, or by designing immutable and thread-safe systems.”

---

If you want next, I can ask you a **very common follow-up interview question:**
👉 “How is race condition different from deadlock and livelock?”
