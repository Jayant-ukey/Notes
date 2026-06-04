# Que: Can you tell me a few features of Java 8?

For a **5-year experienced Java/Spring Boot interview**, this answer should not be just a list—you should explain **features + why they matter in real projects**.

---

# Java 8 Features (Very Important)

Java SE 8 is one of the most important Java versions used in enterprise applications.

It introduced **functional programming concepts into Java**.

---

# 1. Lambda Expressions ⭐ (Most Important)

## What it is?

A shorter way to represent anonymous functions.

---

## Before Java 8

```java id="a1b2c3"
Runnable r = new Runnable() {
    public void run() {
        System.out.println("Running");
    }
};
```

---

## After Java 8

```java id="d4e5f6"
Runnable r = () -> System.out.println("Running");
```

---

## Why important?

* Reduces boilerplate code
* Enables functional programming
* Used heavily in Streams API

---

# 2. Streams API ⭐

Used for processing collections in a functional way.

---

## Example

```java id="x1y2z3"
List<Integer> list = Arrays.asList(1,2,3,4,5);

list.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .forEach(System.out::println);
```

---

## Benefits

* Declarative style
* Easy parallel processing
* Cleaner code

---

# 3. Functional Interfaces

An interface with exactly one abstract method.

Example:

```java id="m4n5o6"
@FunctionalInterface
interface MyFunction {
    void execute();
}
```

Common built-in ones:

* Predicate
* Function
* Consumer
* Supplier

---

# 4. Default Methods in Interfaces

Interfaces can now have method implementations.

```java id="p7q8r9"
interface Vehicle {
    default void start() {
        System.out.println("Vehicle starting");
    }
}
```

---

## Why added?

To support backward compatibility without breaking existing implementations.

---

# 5. Method References

Shorter way to call methods.

```java id="z1a2b3"
list.forEach(System.out::println);
```

Instead of:

```java id="c4d5e6"
list.forEach(x -> System.out.println(x));
```

---

# 6. Optional Class ⭐

Used to avoid NullPointerException.

```java id="f7g8h9"
Optional<String> name = Optional.ofNullable(getName());

name.ifPresent(System.out::println);
```

---

## Benefits

* Avoid null checks
* More readable code
* Better API design

---

# 7. New Date & Time API

Replaced old `Date` and `Calendar`.

Package:

```text id="k8l9m0"
java.time
```

Example:

```java id="n1o2p3"
LocalDate date = LocalDate.now();
LocalDateTime now = LocalDateTime.now();
```

---

## Why better?

* Immutable
* Thread-safe
* More readable

---

# 8. Nashorn JavaScript Engine (Removed later)

Allowed Java to execute JavaScript code.

(Not widely used in modern systems)

---

# 9. Parallel Array Operations

```java id="q1w2e3"
Arrays.parallelSort(array);
```

Used for performance improvements.

---

# Real Project Usage (Important for Interviews)

In real-world Spring Boot applications:

* Lambda expressions → used in streams, repository filtering
* Streams API → data processing, transformation
* Optional → handling null responses from DB
* Date API → timestamps in microservices
* Functional interfaces → strategy patterns

---

# Example in Spring Boot

```java id="r4t5y6"
List<Employee> result = employees.stream()
    .filter(e -> e.getSalary() > 50000)
    .collect(Collectors.toList());
```

---

# Why Java 8 is Important?

* Introduced functional programming
* Reduced boilerplate code
* Improved collection processing
* Became foundation for modern Java (Spring Boot heavily uses it)

---

# Interview-Ready Answer

> Java 8 introduced major features like Lambda expressions, Streams API, Functional Interfaces, Default methods in interfaces, Optional class, and a new Date and Time API. These features brought functional programming capabilities into Java, making code more concise, readable, and expressive. In enterprise applications like Spring Boot, Java 8 features are widely used for collection processing, null safety using Optional, and cleaner code using streams and lambdas.

---

# One-Line Answer

> Java 8 introduced functional programming features in Java such as Lambdas, Streams API, Functional Interfaces, Default methods, Optional class, and a new Date-Time API, which significantly improved code readability and reduced boilerplate.
