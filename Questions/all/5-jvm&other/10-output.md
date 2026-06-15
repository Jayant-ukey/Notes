# Que - What will be the output of the following code snippet?

public class Test {
    public static void main(String[] args) {
        for(int i = 0; i < 5; ) {
            System.out.println("Hello World");
        }
    }
}

---

The code will print **"Hello World" indefinitely** (an infinite loop).

### Why?

The `for` loop has this structure:

```java
for(int i = 0; i < 5; ) {
    System.out.println("Hello World");
}
```

A `for` loop consists of:

```java
for(initialization; condition; update)
```

Here:

* **Initialization:** `int i = 0`
* **Condition:** `i < 5`
* **Update:** *(missing)*

Since `i` is initialized to `0` and never changes, the condition `i < 5` is always `true`.

Therefore, the loop never terminates and repeatedly executes:

```java
System.out.println("Hello World");
```

### Output

```text
Hello World
Hello World
Hello World
Hello World
...
```

The program continues printing `"Hello World"` forever (until it is manually stopped or runs out of resources).
