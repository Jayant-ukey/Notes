# Que - What happens if you try to start an already started thread?

### ✅ **Interview Answer (5 YOE level)**

If you try to start a thread that has already been started, Java will throw a **runtime exception**:

> **`java.lang.IllegalThreadStateException`**

---

## 🔍 **Why does this happen?**

In Java, a thread can be started **only once** using `start()`.

When you call `start()`:

* JVM creates a **new OS-level thread**
* Thread transitions from **NEW → RUNNABLE**

After that, the thread moves through its lifecycle and eventually reaches:

* **TERMINATED state**

At this point, the thread is no longer eligible for restart.

So if you call `start()` again, JVM detects:
👉 “This thread has already been started”
👉 and throws an exception.

---

## ❌ **Example**

```java id="q8m1pk"
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running");
    }
}

public class Test {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();

        t1.start(); // ✅ first time works

        t1.start(); // ❌ second time throws exception
    }
}
```

---

## ⚠️ **Exception thrown**

```text
java.lang.IllegalThreadStateException
```

---

## 🧠 **Key Interview Points**

Interviewers expect you to clearly mention:

* `start()` can be called only **once per thread object**
* Second call results in **IllegalThreadStateException**
* Because thread lifecycle is **one-time execution**
* JVM internally checks thread state before starting

---

## 🔥 **Important clarification (often asked follow-up)**

### ❓ Can we call `run()` again?

Yes — but:

* It does NOT create a new thread
* It behaves like a normal method call

---

## 🚀 **Real-world insight (5 YOE expectation)**

In enterprise systems:

* We never reuse Thread objects
* We use:

  * `ExecutorService`
  * Thread pools
  * `CompletableFuture`

Because they handle reuse safely and avoid this issue completely.

---

## ⭐ **Strong closing statement**

“If we try to call `start()` on an already started thread, JVM throws `IllegalThreadStateException` because a thread in Java is designed for single execution only, and its lifecycle does not allow restarting once it has been started or terminated.”

---
