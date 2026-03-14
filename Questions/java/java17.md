# What new features were introduced in Java 17?



> Java 17 is a Long-Term Support (LTS) release that introduced several important features such as Sealed Classes, Pattern Matching for switch (preview), Records improvements, Strong encapsulation of JDK internals, a new macOS rendering pipeline, and performance improvements. It also removed some deprecated APIs and improved security and stability.

---

# Key Features Introduced in Java 17

## 1️⃣ Sealed Classes

Sealed classes allow you to **control which classes can extend or implement them**.

Example:

```java
public sealed class Shape permits Circle, Rectangle {
}

final class Circle extends Shape {}
final class Rectangle extends Shape {}
```

Benefits:

* Better **control over inheritance**
* Useful for **domain models**
* Works well with **pattern matching**

---

## 2️⃣ Pattern Matching for switch (Preview)

Enhances the `switch` statement to support **type patterns**.

Example:

```java
switch (obj) {
    case Integer i -> System.out.println("Integer: " + i);
    case String s -> System.out.println("String: " + s);
    default -> System.out.println("Unknown type");
}
```

Benefits:

* Cleaner code
* Less casting

---

## 3️⃣ Records (Standardized)

Records were introduced earlier but stabilized in later versions. They help create **immutable data classes with minimal boilerplate code**.

Example:

```java
public record Person(String name, int age) {}
```

Automatically generates:

* constructor
* getters
* `equals()`
* `hashCode()`
* `toString()`

---

## 4️⃣ Strong Encapsulation of JDK Internals

Internal JDK APIs are **strongly encapsulated**, improving security and maintainability.

Meaning:

* Developers cannot easily access **internal JDK classes** anymore.

---

## 5️⃣ New macOS Rendering Pipeline

Java 17 introduced a **Metal-based rendering pipeline for macOS**, replacing the old OpenGL pipeline.

Benefits:

* Better graphics performance
* Improved compatibility with modern macOS systems

---

## 6️⃣ Removal of Deprecated APIs

Some old APIs were removed, such as:

* Applet APIs
* Deprecated security manager components

This helps **clean up legacy code**.

---

# Bonus Points (Good to Mention in Interview)

You can also add:

* LTS release (very important for enterprises)
* Performance improvements
* Security enhancements
* Garbage collector improvements
