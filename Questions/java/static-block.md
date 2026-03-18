**Que:** What is static block in Java?


**Answer:** A **static block in Java** is a block of code inside a class that is **executed only once when the class is loaded into memory**, before any object of the class is created.

It is mainly used to **initialize static variables** or perform **one-time setup tasks**.

**Key points:**

* It is declared using the `static` keyword.
* It runs **only once** when the class is loaded by the JVM.
* It executes **before the main method** and before any constructor.
* It is typically used to initialize **static data members**.

**Example:**

```java
class Test {
    static int a;

    static {
        a = 10;
        System.out.println("Static block executed");
    }

    public static void main(String[] args) {
        System.out.println("Value of a: " + a);
    }
}
```

**Output:**

```
Static block executed
Value of a: 10
```

So, the static block runs first when the class is loaded.

---



## 1️⃣ Can a class have multiple static blocks?

**Answer:** Yes, a class can have **multiple static blocks** and they are executed **in the order they appear in the class**.

```java
class Test {
    static {
        System.out.println("First static block");
    }

    static {
        System.out.println("Second static block");
    }

    public static void main(String[] args) {
        System.out.println("Main method");
    }
}
```

**Output**

```
First static block
Second static block
Main method
```

---

## 2️⃣ Can we access non-static variables inside a static block?

**Answer:** **No**, because static blocks run **before any object is created**, so they can only directly access **static variables and methods**.

---

## 3️⃣ When exactly does a static block execute?

**Answer:** It executes **once when the class is loaded into memory by the JVM**, before:

* object creation
* constructors
* the `main()` method

---

## 4️⃣ Can we write a static block inside a method?

**Answer:** **No.** Static blocks can only be written **inside a class**, not inside methods or constructors.

---

## 5️⃣ Can a static block throw an exception?

**Answer:** Yes, but it must be **handled inside the static block**. If not handled, the program will throw **ExceptionInInitializerError**.

Example:

```java
class Test {
    static {
        int x = 10 / 0;
    }
}
```

This will cause **ExceptionInInitializerError**.

---

## 6️⃣ Can we use a static block without a main method?

**Answer:** Yes, but the class must still be **loaded by JVM**. If the class is never used, the static block won't execute.

Example:

```java
class Test {
    static {
        System.out.println("Static block executed");
    }
}
```

If you run this class directly, **nothing happens** because there is no `main()`.

---

## 7️⃣ Can constructors execute before static blocks?

**Answer:** **No.** Static blocks always execute **before constructors**, because constructors run when an **object is created**, but static blocks run **when the class loads**.

---

## 8️⃣ What is the execution order in Java?

**Answer:**

1. Static variables initialization
2. Static blocks
3. `main()` method
4. Constructors
5. Instance blocks (if any)

---


**"What is the difference between static block and instance block?"**

Short answer:

| Static Block               | Instance Block                    |
| -------------------------- | --------------------------------- |
| Runs once when class loads | Runs every time object is created |
| Uses `static {}`           | Uses `{}`                         |
| Executes before main       | Executes before constructor       |

---


