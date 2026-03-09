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

