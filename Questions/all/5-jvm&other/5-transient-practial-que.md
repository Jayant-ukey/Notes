# Que - If you mark a field as transient in a Serializable class, what happens when the object is deserialized, and how would you restore that field?

### 1. Direct Answer (What)

If a field is marked as `transient` in a `Serializable` class, it is **not serialized at all**.
So during **deserialization**, that field is **not restored from the byte stream** and instead gets its **default value**:

* `null` for objects
* `0` for numbers
* `false` for boolean

To restore such a field, you must **manually reinitialize it**, typically using:

* `readObject()` (custom deserialization logic)
* Recomputing the value after deserialization
* Fetching it again from an external source (DB/cache/config)

---

### 2. Internal Working (How)

During serialization:

* JVM skips `transient` fields completely while writing object state

During deserialization:

* JVM reconstructs object using `ObjectInputStream`
* Only non-transient fields are populated
* Transient fields are initialized with default JVM values

So effectively:

> The field never existed in the serialized form

---

### 3. How to Restore Transient Fields

#### Approach 1: Using `readObject()` (Most common in interviews)

You can customize deserialization:

```java id="k3m8xz"
class User implements Serializable {

    private String username;
    private transient String password;

    private void readObject(ObjectInputStream in)
            throws IOException, ClassNotFoundException {
        in.defaultReadObject(); // restore normal fields

        // manually restore transient field
        this.password = "defaultPassword"; // or fetch from secure store
    }
}
```

---

#### Approach 2: Recompute after deserialization

If the transient field is derived:

```java id="r9p2aa"
class Order implements Serializable {

    private double price;
    private int quantity;

    private transient double total;

    private void readObject(ObjectInputStream in)
            throws IOException, ClassNotFoundException {
        in.defaultReadObject();
        this.total = price * quantity;
    }
}
```

---

#### Approach 3: Fetch from external source

In real systems:

* DB
* Cache (Redis)
* Configuration service

```java id="t6x1bb"
this.password = userService.fetchPassword(userId);
```

---

#### Approach 4: Lazy initialization

Sometimes we don’t restore immediately:

```java id="y8k3cc"
public String getPassword() {
    if (password == null) {
        password = loadFromSecureStore();
    }
    return password;
}
```

---

### 4. Practical Experience (Real-world Usage)

In production Spring Boot systems, transient fields are commonly used for:

* **Sensitive data** (passwords, tokens)
* **Non-serializable resources** (DB connections, threads)
* **Derived fields** (totals, computed metrics)

Typical scenario:

* Object stored in Redis or HTTP session
* After deserialization, transient fields must be recomputed or re-fetched

Example:

* Shopping cart total is often transient and recalculated from items list

---

### 5. Best Practices / Performance Considerations

* Always call `in.defaultReadObject()` in custom deserialization
* Avoid storing sensitive data directly in memory unless needed
* Prefer recomputation over storing redundant transient state
* Be careful: restoring from external sources inside `readObject()` can:

  * Increase latency
  * Introduce hidden dependencies
* Prefer DTOs for serialization-heavy systems instead of complex entity graphs

---

### 6. Key Points Interviewers Look For

* Transient fields are **not serialized**
* They become **default values after deserialization**
* JVM skips them during object serialization process
* Knowledge of `readObject()` for restoration
* Ability to explain real-world restoration strategies:

  * recomputation
  * external fetch
  * lazy loading
* Awareness of trade-offs (performance, security, design complexity)

---

### 7. Common Follow-up Questions

1. What is the role of `readObject()` and `writeObject()`?
2. Can we serialize a transient field manually?
3. What happens if a transient field is `final`?
4. Difference between transient and static fields?
5. When should you avoid using transient?
6. How does Jackson (JSON serialization) treat transient fields?
7. What happens in distributed cache (Redis) for transient fields?
8. Can constructors help in restoring transient fields?

---

### One-Line Senior-Level Summary

> "Transient fields are skipped during serialization and reinitialized to default values during deserialization, and they can be restored manually using custom deserialization logic like `readObject()`, recomputation, or external data sources depending on the use case."
