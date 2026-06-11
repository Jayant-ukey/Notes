# Que- What are the different states of a thread in Java? OR What are the different phases/states in a Thread Life Cycle?

This is a **very important interview question** — interviewers expect both **state names + brief explanation + flow understanding**.

---

## ✅ **Thread Life Cycle / States in Java**

A thread in Java goes through **different states during its execution**, defined in the `Thread.State` enum.

There are **6 main states**:

---

## **1. NEW**

* Thread is created but not yet started.
* `start()` has not been called.

```java
Thread t = new Thread();
```

👉 At this stage, thread exists in memory but not scheduled.

---

## **2. RUNNABLE**

* After calling `start()`, thread enters RUNNABLE state.
* It is **ready to run or currently running** depending on CPU scheduling.

👉 Important interview point:
RUNNABLE = “Ready + Running” (JVM does not separate them strictly)

---

## **3. BLOCKED**

* Thread is waiting to acquire a lock (monitor).
* Happens in **synchronized blocks/methods**.

### Example:

If one thread holds a lock, others trying to enter synchronized code go to BLOCKED.

---

## **4. WAITING**

* Thread is waiting indefinitely for another thread to perform an action.

### Happens using:

* `Object.wait()`
* `Thread.join()`
* `LockSupport.park()`

👉 No timeout here.

---

## **5. TIMED_WAITING**

* Thread waits for a specified time.

### Happens using:

* `sleep(time)`
* `wait(time)`
* `join(time)`

👉 After timeout, thread moves back to RUNNABLE.

---

## **6. TERMINATED (or DEAD)**

* Thread has completed execution or exited `run()` method.
* Cannot be restarted.

---

## 📌 **Thread Life Cycle Flow (Important for interviews)**

```
NEW
  ↓ start()
RUNNABLE
  ↓
RUNNING (part of RUNNABLE conceptually)
  ↓
BLOCKED / WAITING / TIMED_WAITING
  ↓
RUNNABLE
  ↓
TERMINATED
```

---

## 🧠 **Key Interview Insights (what interviewer looks for)**

### 1. Difference between WAITING and TIMED_WAITING

* WAITING → indefinite
* TIMED_WAITING → time-based

### 2. BLOCKED vs WAITING

* BLOCKED → waiting for lock (synchronization issue)
* WAITING → waiting for signal/notification

---

## ⚡ **Real-world Spring Boot relevance**

In backend systems:

* Threads enter **BLOCKED** due to synchronized DB access or locks
* **WAITING/TIMED_WAITING** happens in:

  * Thread pools
  * API calls with timeout
  * Async processing (`@Async`, CompletableFuture)

---

## ⭐ **Strong Interview Closing Statement**

“In Java, a thread transitions through New, Runnable, Blocked, Waiting, Timed Waiting, and Terminated states. Understanding these states is important for debugging concurrency issues like deadlocks, thread contention, and performance bottlenecks in enterprise applications.”
