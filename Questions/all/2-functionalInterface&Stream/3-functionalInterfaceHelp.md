# Que: If you are designing a custom operation to be passed to multiple utility methods, how can a functional interface help, and how would you use a lambda expression?

For interviews, it's usually better to answer at a **conceptual level first**, then give a simple example if asked. Interviewers are often checking whether you understand *why* functional interfaces exist, not whether you can write a large code sample.

A strong interview answer would be:

> A functional interface helps when I want to pass a piece of behavior or logic to a method. Since it has only one abstract method, it can be implemented using a lambda expression. This allows me to write generic utility methods and pass different operations without creating multiple implementation classes.
>
> For example, if I have a utility method that processes two numbers, I can define a functional interface called `Operation` with a method like `perform(int a, int b)`. Then, while calling the utility method, I can pass different lambda expressions such as `(a, b) -> a + b` for addition or `(a, b) -> a * b` for multiplication. This makes the code more reusable, concise, and flexible.

If the interviewer asks for a code example, then write:

```java
@FunctionalInterface
interface Operation {
    int perform(int a, int b);
}

public static int execute(int a, int b, Operation op) {
    return op.perform(a, b);
}

// Usage
execute(10, 5, (a, b) -> a + b); // Addition
execute(10, 5, (a, b) -> a * b); // Multiplication
```

### What interviewers expect to hear

Mention these keywords:

* **Single Abstract Method (SAM)**
* **Lambda expression**
* **Pass behavior as a parameter**
* **Code reusability**
* **Less boilerplate code**
* **Strategy pattern-like behavior**

A concise 30-second answer is often ideal in a Java interview unless they specifically ask you to elaborate.
