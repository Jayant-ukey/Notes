# Que- why can't a thread be restarted?

A thread cannot be restarted in Java because of how the **JVM thread lifecycle and underlying OS thread model are designed**.

---

## ✅ **Interview-level Answer**

Once a thread completes execution, it enters the **TERMINATED state**, and it cannot transition back to **NEW or RUNNABLE state**. Therefore, calling `start()` again is not allowed.

---

## 🔍 **Core Reasons (what interviewer expects)**

### **1. JVM does not allow state reset**

A thread object in Java has a strict lifecycle:

```
NEW → RUNNABLE → RUNNING → TERMINATED
```

Once it reaches **TERMINATED**, the lifecycle is over. There is **no backward transition** defined in the JVM specification.

---

### **2. Native OS thread is destroyed**

Java threads are mapped to **native OS threads**.

When execution completes:

* OS releases thread stack memory
* Registers and execution context are destroyed
* Thread ID may be reclaimed

So there is nothing left to “restart”.

---

### **3. Thread object is not reusable by design**

A `Thread` object holds:

* Execution state
* Stack
* Internal JVM metadata

After completion:

* These resources are cleaned up
* The object becomes **invalid for execution again**

---

### **4. start() creates a new OS thread**

The `start()` method is designed to:

* Create a **new native thread**
* Allocate fresh stack memory
* Call `run()` once

So calling `start()` again would mean:
👉 “reuse same Java Thread object but create another OS thread”
which violates internal consistency.

---

## ❌ What happens if you try?

```java
t1.start();
t1.start(); // ❌ IllegalThreadStateException
```

Because JVM explicitly checks:

> “Has this thread already been started?”

If yes → exception thrown.

---

## 🧠 **Important Interview Insight**

You should clearly say this:

> “Thread objects in Java are designed for one-time execution. Once started, they cannot be restarted because the underlying OS thread resources are destroyed and the JVM does not support resetting thread state.”

---

## 🚀 **Real-world analogy (good for interviews)**

Think of a thread like a **work order ticket**:

* You create a ticket → assign work → complete it → close it
* You cannot reopen the same ticket; you must create a new one

Same in Java:
👉 New execution = new Thread object

---

## ⭐ **Best closing statement**

“A thread cannot be restarted because its lifecycle is one-time by design; once execution completes, the JVM and OS release all resources associated with it, and the thread transitions permanently to the TERMINATED state. Hence, a new thread instance is required for re-execution.”

