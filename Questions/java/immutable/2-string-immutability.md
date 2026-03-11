**Que : Explain String immutability with example**
### String Immutability in Java

In Java, **java.lang.String** is **immutable**, which means **once a String object is created, its value cannot be changed**. Any operation that seems to modify a string actually **creates a new String object instead of modifying the existing one**.

---

## 1️⃣ Simple Example

```java
String s = "Hello";
s.concat(" World");

System.out.println(s);
```

**Output**

```
Hello
```

### Explanation

* `"Hello"` creates a String object.
* `concat(" World")` creates a **new String object** `"Hello World"`.
* Since the result is **not assigned to any variable**, the original `s` remains `"Hello"`.

---

## 2️⃣ Example with Assignment

```java
String s = "Hello";
s = s.concat(" World");

System.out.println(s);
```

**Output**

```
Hello World
```

### Explanation

1. `"Hello"` is created.
2. `concat()` creates a **new object `"Hello World"`**.
3. The reference `s` now points to the new object.

The original `"Hello"` object still exists in memory (especially in the **String Pool**).

---

## 3️⃣ Memory Representation

```
Before concat():

s ---> "Hello"

After concat():

"Hello"         "Hello World"
   ^                 ^
   |                 |
 (old object)      s points here
```

The original string **never changes**.

---

## 4️⃣ Why Java Made String Immutable

Main reasons:

1️⃣ **Security**
Strings are used in file paths, URLs, database connections.

2️⃣ **Thread Safety**
Immutable objects are naturally **thread-safe**.

3️⃣ **String Pool Optimization**
Multiple variables can reference the same string.

```java
String a = "Java";
String b = "Java";
```

Both may point to the **same memory location** in the pool.

4️⃣ **Hashcode Caching**
Important when used as keys in collections like **java.util.HashMap**.

---

## 5️⃣ Interview One-Line Answer

You can say:

> In Java, String is immutable, meaning its value cannot be changed after creation. Any modification operation creates a new String object instead of altering the existing one.

