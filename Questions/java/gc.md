

**Garbage Collection (GC)** in Java is the process by which the **JVM automatically manages memory** by identifying and removing objects that are no longer in use, freeing up heap space.


### 🔹 How it works (important for interview)

Garbage collection mainly works on the concept of **reachability**:

* Objects referenced from:

  * stack variables
  * static fields
  * active threads
    are considered **reachable**
* Unreachable objects → eligible for GC

---

### 🔹 Heap structure (very important for 5+ years)

JVM heap is divided into:

1. **Young Generation**

   * Eden space
   * Survivor spaces (S0, S1)
   * New objects are created here
   * Minor GC happens frequently

2. **Old (Tenured) Generation**

   * Long-lived objects move here
   * Major/Full GC happens here (costly)

3. *(Optional mention)* Metaspace (for class metadata)

---

### 🔹 Types of Garbage Collection

* **Minor GC** → Cleans Young Generation
* **Major GC / Full GC** → Cleans Old Generation (slower, impacts performance)

---

### 🔹 Common GC Algorithms (this is where you stand out)

* Mark and Sweep
* Mark and Compact
* Generational GC
* G1 GC (widely used in modern JVMs)

---

### 🔹 Important concepts

* **Stop-The-World (STW)** → Application pauses during GC
* **GC Roots** → Starting points for reachability
* **Memory leaks can still happen** (if references are unintentionally held)

---

### 🔹 Practical angle (very important)

You can add:

* We can’t force GC, but can suggest using `System.gc()` (not guaranteed)
* GC tuning is done using JVM options (e.g., `-Xms`, `-Xmx`, GC algorithms)
* Performance issues often relate to frequent Full GC

---

### 🔹 Short crisp version (if interviewer wants quick answer)

“Garbage Collection in Java is an automatic memory management process where the JVM removes unreachable objects from heap memory. It works based on reachability analysis, divides memory into generations like Young and Old, and uses algorithms like Mark-Sweep or G1 to reclaim memory efficiently.”


