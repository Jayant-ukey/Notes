# Que: If you are designing a custom operation to be passed to multiple utility methods, how can a functional interface help, and how would you use a lambda expression?

This is a good Java 8 interview question because it tests whether you understand the practical use of **functional interfaces** and **lambda expressions**.

### Concept

A **functional interface** defines a contract for a single operation (it contains exactly one abstract method).

Instead of creating multiple classes that implement the interface, we can use **lambda expressions** to provide the implementation inline and pass behavior as a parameter.

This allows utility methods to become more generic and reusable.

---

### Example

Suppose I want to perform different mathematical operations using the same utility method.

#### Functional Interface

```java
@FunctionalInterface
public interface Operation {
    int perform(int a, int b);
}
```

#### Utility Class

```java
public class Calculator {

    public static int execute(int a, int b, Operation operation) {
        return operation.perform(a, b);
    }
}
```

#### Using Lambda Expressions

```java
public class Main {
    public static void main(String[] args) {

        int sum = Calculator.execute(10, 5, (a, b) -> a + b);

        int multiplication = Calculator.execute(10, 5, (a, b) -> a * b);

        int max = Calculator.execute(10, 5, (a, b) -> Math.max(a, b));

        System.out.println(sum);            // 15
        System.out.println(multiplication); // 50
        System.out.println(max);            // 10
    }
}
```

---

### Why is this useful?

Without lambdas, we would need separate implementation classes:

```java
class AddOperation implements Operation {
    public int perform(int a, int b) {
        return a + b;
    }
}
```

With lambdas, we can pass the implementation directly:

```java
(a, b) -> a + b
```

This reduces boilerplate code and makes the utility method highly reusable.

---

### Interview Answer (Concise)

> If I need to pass different behaviors to multiple utility methods, I can define a functional interface with a single abstract method representing that behavior. The utility method accepts the functional interface as a parameter. Using Java 8 lambda expressions, I can provide the implementation inline without creating separate classes. This promotes code reuse, makes the code cleaner, and follows a strategy-like approach where behavior can be changed at runtime.
