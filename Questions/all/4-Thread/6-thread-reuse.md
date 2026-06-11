# Que- Can a thread be started again after it has completed execution?

No — **a thread cannot be restarted once it has completed execution in Java**.

---

## ✅ **Answer (Interview Expectation)**

Once a thread reaches the **TERMINATED (dead) state**, it cannot be started again by calling `start()`.

If you try to restart it, Java will throw:

```
java.lang.IllegalThreadStateException
```

---

## 🔍 **Why is it not allowed?**

When a thread finishes execution:

* Its **call stack is destroyed**
* OS-level thread resources are released
* JVM marks it as **terminated**
* It is considered a **one-time execution unit**

So logically, restarting it would violate thread lifecycle rules.

---

## ❌ **Example (will fail)**

```java id="t9x2k1"
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running");
    }
}

public class Test {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();

        t1.start(); // first time OK

        t1.start(); // ❌ Runtime exception
    }
}
```

---

## ⚠️ **Exception thrown**

```
java.lang.IllegalThreadStateException
```

---

## 💡 **Correct approach if you want to run again**

You must create a **new thread object**:

```java id="k3m8qp"
MyThread t1 = new MyThread();
t1.start();

MyThread t2 = new MyThread(); // new instance
t2.start();
```

---

## 🧠 **Key Interview Points**

Interviewers expect you to say:

* Thread is **not reusable**
* Once terminated → cannot be restarted
* `start()` can only be called **once per thread object**
* For reuse, use **ExecutorService / thread pools**

---

## 🚀 **Real-world insight (important for 5 YOE)**

In enterprise systems:

* We never rely on reusing raw Thread objects
* Instead we use:

  * Thread pools (`ExecutorService`)
  * Spring `@Async`
  * CompletableFuture

Because thread creation is expensive, and pools manage reuse efficiently.

---

## ⭐ **Strong closing line**

“A thread in Java is a one-time execution unit; once it reaches the terminated state, it cannot be restarted, and we should instead use thread pools for reusable execution in real-world applications.”

