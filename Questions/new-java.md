# Que: Functional Interface vs Marker Interface

## Marker Interface

A **marker interface** in Java (also known as a **tagging interface**) is an interface that contains **no methods, fields, or constants**.

It is used strictly to deliver **metadata** to the Java Virtual Machine (JVM), compiler, or a framework, signaling that a class possesses a special behavior or capability.

### How It Works

When a class implements a marker interface, it effectively applies a **"tag"** to itself.

The environment or application logic checks for this tag at runtime using the `instanceof` keyword or **reflection** to decide how to treat the object.

### Built-in Examples in Java

Java provides several standard built-in marker interfaces:

1. **`java.io.Serializable`**
   - Marks a class so its objects can be converted into a byte stream.
   - This allows objects to be saved to a file or sent over a network.

2. **`java.lang.Cloneable`**
   - Signals that the `Object.clone()` method is valid to call on instances of the class.
   - If `clone()` is called without implementing `Cloneable`, it throws a `CloneNotSupportedException`.

3. **`java.rmi.Remote`**
   - Flags an interface as capable of being invoked from a remote virtual machine.

## Functional Interface vs Marker Interface

| Feature | Functional Interface | Marker Interface |
|---|---|---|
| Abstract Methods | Exactly one | Zero |
| Purpose | Represents a single behavior/function | Provides metadata or a tag |
| Lambda Support | Yes | No |
| `@FunctionalInterface` | Can be used | Cannot be used |
| Example | `Runnable`, `Comparator`, `Predicate` | `Serializable`, `Cloneable` |
| Main Usage | Lambda expressions and method references | Identifying or marking classes |
| Methods | Must contain exactly one abstract method | Contains no methods |

---

### Question

**Can we overload the `main()` method in Java? Which one does JVM execute?**

### Answer

Yes, we can **overload the `main()` method** in Java.

Like any other method, we can create multiple versions of `main()` as long as they have **different parameter types or a different number of parameters**.

However, the JVM will always execute the standard entry point with the specific signature:

```java
public static void main(String[] args)

public class MainOverloadDemo {

    // 1. The original entry point executed by the JVM
    public static void main(String[] args) {
        System.out.println("JVM started here: main(String[] args)");

        // Explicitly calling the overloaded versions
        main(42);
        main("Hello Java");
    }

    // 2. Overloaded main method with an int parameter
    public static void main(int number) {
        System.out.println("Overloaded main with int: " + number);
    }

    // 3. Overloaded main method with a single String parameter
    public static void main(String message) {
        System.out.println("Overloaded main with String: " + message);
    }
}

