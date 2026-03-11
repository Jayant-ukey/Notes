**Que: “What is immutability?”**

### 1️⃣ Definition (Expected core answer)

**Immutability** means **an object’s state cannot be changed after it is created**.

Once an immutable object is initialized, **its data remains constant for the entire lifetime of the object**.

Example: In Java, the class **java.lang.String** is immutable.

```java
String s = "Hello";
s.concat(" World");
System.out.println(s);
```

Output:

```
Hello
```

Even though `concat()` was called, **a new String object is created**, and the original `s` remains unchanged.

---

### 2️⃣ Key Characteristics of an Immutable Class

To make a class immutable in Java:

1. Declare the class as `final` (so it cannot be subclassed).
2. Make all fields `private` and `final`.
3. Do not provide setter methods.
4. Initialize fields only through the constructor.
5. If fields are mutable objects, return **defensive copies**.

Example:

```java
final class Person {
    private final String name;
    private final int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

Here, the `Person` object's state **cannot be changed after creation**.

---

### 3️⃣ Why Immutability is Important (Interviewers like this part)

Advantages:

* ✅ **Thread-safe** (no synchronization needed)
* ✅ **Safer in concurrent environments**
* ✅ **Objects can be cached and reused**
* ✅ **Good for keys in HashMap**

Example: **java.lang.String**, **java.lang.Integer**, and **java.time.LocalDate** are immutable.

---

### 4️⃣ Short Interview Answer (Best 20-second answer)

If they expect a **quick answer**, you could say:

> **Immutability means an object cannot be modified after it is created. Its state remains constant throughout its lifetime. In Java, classes like String are immutable, meaning any modification creates a new object instead of changing the existing one. Immutable objects are thread-safe and widely used in concurrent programming.**
