# Que- How does garbage collection work in Java?

### ✅ **How does Garbage Collection work in Java? (Interview-level answer – 5 YOE)**

Garbage Collection (GC) is an automatic memory management mechanism in Java that **identifies and removes objects that are no longer reachable or used by the application**, thereby freeing heap memory.

The main goal of GC is to **prevent memory leaks caused by manual memory management and simplify application development**.

---

## 🔍 **How does Garbage Collection work?**

Java stores objects in the **Heap Memory**.

When an object is no longer referenced by any active part of the application, it becomes **eligible for garbage collection**.

### Example:

```java id="gc1"
Employee emp = new Employee();

emp = null;
```

Here, the `Employee` object has no reference pointing to it, so it becomes eligible for GC.

---

## 🧠 **Important Interview Point**

### GC does NOT immediately remove the object.

Many candidates say:

> "When we set an object to null, GC removes it."

This is incorrect.

Correct statement:

> "Setting an object reference to null only makes the object eligible for garbage collection. The JVM decides when to actually reclaim the memory."

---

## 🔍 **How does GC identify unused objects?**

Java GC starts from special references called **GC Roots**, such as:

* Local variables in stack frames
* Active threads
* Static variables
* JNI references

It performs **reachability analysis**:

### Reachable Object

```text
GC Root → Object A → Object B
```

Objects A and B are reachable and remain in memory.

### Unreachable Object

```text
Object C
```

No path from any GC Root.

👉 Eligible for garbage collection.

---

## 🔍 **Generational Heap Model (Very Important)**

Most modern GCs use the observation:

> "Most objects die young."

Heap is divided into:

### 1. Young Generation

Stores newly created objects.

Contains:

* Eden Space
* Survivor Spaces (S0, S1)

### 2. Old/Tenured Generation

Stores long-lived objects.

### 3. Metaspace

Stores class metadata.

(Java 8+ replaced PermGen with Metaspace.)

---

## 🔍 **Types of Garbage Collection Events**

### Minor GC

* Cleans Young Generation
* Happens frequently
* Usually fast

### Major/Full GC

* Cleans Old Generation (and sometimes entire heap)
* More expensive
* Can impact application performance

---

## 🔍 **Common Garbage Collectors**

Depending on JVM version:

* Serial GC
* Parallel GC
* G1 GC (default in modern JVMs)
* ZGC
* Shenandoah GC

For most enterprise applications today, G1 GC is commonly used.

---

## ⚠️ **Can we force Garbage Collection?**

We can request GC using:

```java id="gc2"
System.gc();
```

But:

> It is only a request to the JVM, not a guarantee.

The JVM may ignore it.

---

## 🚀 **Real-world Spring Boot perspective**

As a backend developer, we monitor GC because:

* Excessive Full GC can cause latency issues.
* Memory leaks may lead to OutOfMemoryError.
* Large object creation can increase GC pressure.

Common monitoring tools:

* JVM GC logs
* JVisualVM
* Java Mission Control
* Prometheus + Grafana
* APM tools like Dynatrace/AppDynamics

---

## 🧠 **What interviewers expect you to mention**

You should cover:

✅ Automatic memory management
✅ Objects become eligible when unreachable
✅ Reachability analysis from GC Roots
✅ Young Generation and Old Generation
✅ Minor GC vs Full GC
✅ `System.gc()` is only a request
✅ G1 GC is commonly used today

---

## ⭐ **Strong closing statement**

"Java Garbage Collection automatically reclaims heap memory occupied by objects that are no longer reachable from GC Roots. Modern JVMs use a generational heap model, performing Minor and Major GC cycles to efficiently manage memory while minimizing application pauses and improving performance."

---

### Follow-up questions interviewers commonly ask after this:

1. **What makes an object eligible for Garbage Collection?**
2. **Can an object with a circular reference be garbage collected?**
3. **What is the difference between Minor GC and Full GC?**
4. **What are Strong, Weak, Soft, and Phantom References?**
5. **What is a memory leak in Java if GC exists?**
