# Que- What are transient fields in Java?

### 1. Direct Answer (What)

A **transient field** in Java is a variable that is **excluded from serialization**.

When an object is serialized (converted into byte stream), any field marked as `transient` is **not saved** and will be assigned its **default value during deserialization**.

---

### 2. Internal Working (How)

When Java serialization happens using `ObjectOutputStream`:

* JVM inspects object fields using reflection
* It writes only non-transient, non-static fields to the stream
* Fields marked as `transient` are skipped completely

During deserialization:

* JVM reconstructs the object
* Transient fields are not restored from stream
* They get default values:

  * `null` for objects
  * `0` for int/long/double
  * `false` for boolean

Example:

```java id="t7qk3m"
class User implements Serializable {
    String username;
    transient String password;
}
```

If serialized and deserialized:

* `username` → restored
* `password` → becomes `null`

---

### 3. Practical Experience (Real-world Usage)

In Spring Boot / enterprise systems, `transient` is commonly used for:

#### 1. Sensitive data protection

* Passwords
* OTPs
* Tokens

Example:

```java id="x1p9aa"
transient String password;
```

So even if object is cached (Redis) or sent over network, sensitive data is not exposed.

---

#### 2. Non-serializable fields

Some fields cannot or should not be serialized:

* Database connections (`Connection`)
* Thread pools
* File handles

Example:

```java id="v8k2lm"
transient Connection connection;
```

---

#### 3. Derived or computed fields

Fields that can be recalculated:

```java id="q9z8rr"
transient double totalPrice;
```

Instead of storing, we recompute after deserialization.

---

### 4. Best Practices / Performance Considerations

#### 1. Use transient for security-sensitive fields

Especially in:

* Session objects
* Cache objects
* DTOs sent over network

---

#### 2. Don’t overuse transient

If a field is needed after deserialization, marking it transient will lead to:

* NullPointerExceptions
* Incorrect state

---

#### 3. Combine with custom serialization if needed

If a transient field still needs controlled handling:

```java id="m2k9pp"
private void writeObject(ObjectOutputStream out) throws IOException
private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException
```

---

#### 4. Remember: static ≠ transient

* `static` fields are not serialized (class-level)
* `transient` is explicitly ignored during serialization

---

### 5. Key Points Interviewers Look For

* Definition: “excluded from serialization”
* Behavior during deserialization (default values)
* Real use cases (security, DB connections, computed fields)
* Difference between `transient` and `static`
* Awareness of Java serialization mechanism
* Ability to relate to Spring Boot / distributed systems

---

### 6. Common Follow-up Questions

1. What is the default value of transient fields after deserialization?
2. Can we serialize transient fields manually?
3. Difference between static and transient fields?
4. Why do we mark passwords as transient?
5. Can we override serialization behavior for transient fields?
6. What happens if a transient field is final?
7. How does Spring Boot handle transient fields in JSON serialization?
8. Are transient fields stored in Redis if using JSON serialization?

---

### One-Line Senior-Level Summary

> "A transient field is a variable that is intentionally excluded from Java serialization, and during deserialization it is reinitialized to default values, typically used for sensitive data or non-serializable resources."
