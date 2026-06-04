# Que:- What are functional interfaces in Java?

# Functional Interfaces in Java

For a **5-year experienced Java/Spring Boot interview**, don’t just define it—explain:

* concept
* rule
* examples
* usage in real systems (Streams, Lambdas, Spring)

---

# What is a Functional Interface?

A **functional interface** is an interface that contains **exactly one abstract method**.

It can have:

* One abstract method (mandatory)
* Any number of default methods
* Any number of static methods

It is mainly used for **Lambda expressions**.

---

## Definition Example

```java id="a1b2c3"
@FunctionalInterface
interface MyFunctionalInterface {
    void execute();
}
```

---

## Why `@FunctionalInterface`?

It is optional, but recommended because:

* Compiler ensures only ONE abstract method
* Prevents accidental mistakes

---

# Lambda Expression Usage

Functional interfaces enable lambdas.

### Without lambda:

```java id="d4e5f6"
MyFunctionalInterface obj = new MyFunctionalInterface() {
    public void execute() {
        System.out.println("Executing...");
    }
};
```

---

### With lambda:

```java id="x1y2z3"
MyFunctionalInterface obj = () -> System.out.println("Executing...");
```

---

# Built-in Functional Interfaces (Very Important)

Java provides ready-made functional interfaces in:

Java SE 8

---

## 1. Predicate<T>

Returns boolean

```java id="m4n5o6"
Predicate<Integer> isEven = x -> x % 2 == 0;
```

---

## 2. Function<T, R>

Takes input, returns output

```java id="p7q8r9"
Function<Integer, Integer> square = x -> x * x;
```

---

## 3. Consumer<T>

Takes input, returns nothing

```java id="z1a2b3"
Consumer<String> print = x -> System.out.println(x);
```

---

## 4. Supplier<T>

Returns value, takes no input

```java id="c4d5e6"
Supplier<Double> randomValue = () -> Math.random();
```

---

# Real Use in Streams API ⭐

Functional interfaces are heavily used in Streams.

```java id="f7g8h9"
List<Integer> list = Arrays.asList(1,2,3,4,5);

list.stream()
    .filter(x -> x % 2 == 0)   // Predicate
    .map(x -> x * x)           // Function
    .forEach(System.out::println); // Consumer
```

---

# Key Rules (Interview Important)

A functional interface:

### ✔ Must have only ONE abstract method

### ✔ Can have multiple default methods

### ✔ Can have static methods

### ✔ Can extend other interfaces (as long as still one abstract method remains)

---

## Example with default + static methods

```java id="g1h2i3"
@FunctionalInterface
interface MyInterface {

    void run(); // single abstract method

    default void log() {
        System.out.println("Logging...");
    }

    static void print() {
        System.out.println("Static method");
    }
}
```

---

# Can a Functional Interface extend another interface?

Yes, but condition applies:

```java id="j4k5l6"
interface A {
    void methodA();
}

@FunctionalInterface
interface B extends A {
    void methodA(); // still single abstract method
}
```

---

# Real-World Usage (Spring Boot Context)

In Spring Boot:

Functional interfaces are used in:

* Stream processing of data from DB
* Lambda expressions in repository filters
* Callback mechanisms
* Custom validations

---

# Example in Business Logic

```java id="q1w2e3"
Predicate<Employee> highSalary = e -> e.getSalary() > 50000;

List<Employee> result = employees.stream()
    .filter(highSalary)
    .collect(Collectors.toList());
```

---

# Why Functional Interfaces were introduced?

Before Java 8:

* Too much boilerplate code
* Anonymous inner classes

After Java 8:

* Cleaner code
* Functional programming support
* Better Streams API integration

---

# Interview-Ready Answer

> A functional interface in Java is an interface that contains exactly one abstract method. It can have default and static methods but only one abstract method. Functional interfaces are used primarily to support lambda expressions and functional programming in Java.
>
> Java provides built-in functional interfaces like Predicate, Function, Consumer, and Supplier, which are heavily used in Streams API. They help write concise and readable code, especially in collections processing and Spring Boot applications.

---

# One-Line Answer

> A functional interface is an interface with exactly one abstract method, used to enable lambda expressions and functional programming in Java.
