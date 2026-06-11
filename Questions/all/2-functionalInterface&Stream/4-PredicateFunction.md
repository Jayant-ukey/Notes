# Que:  What are the roles of Predicate, Function, and Consumer in Java?

Ans:
---

### Predicate

#### What?

`Predicate<T>` is a functional interface that takes an input and returns a boolean value.

#### Why?

It is used when we need to evaluate a condition or filter data based on some criteria.

#### How?

```java
Predicate<Integer> isEven = num -> num % 2 == 0;
boolean result = isEven.test(10);
```

Here, `test()` checks whether the number is even and returns `true` or `false`.

A common use case is filtering collections:

```java
numbers.stream()
       .filter(isEven)
       .forEach(System.out::println);
```

---

### Function

#### What?

`Function<T, R>` is a functional interface that takes one input and returns a transformed output.

#### Why?

It is used when we want to convert or transform data from one form to another.

#### How?

```java
Function<String, Integer> getLength = str -> str.length();
Integer length = getLength.apply("Java");
```

Here, the input is a `String` and the output is an `Integer`.

A common Stream API use case:

```java
names.stream()
     .map(String::length)
     .forEach(System.out::println);
```

---

### Consumer

#### What?

`Consumer<T>` is a functional interface that takes an input and performs an operation without returning anything.

#### Why?

It is used when we need to perform side effects such as logging, printing, saving data, or sending notifications.

#### How?

```java
Consumer<String> print = str -> System.out.println(str);
print.accept("Hello");
```

Here, `accept()` consumes the input and performs an action, but returns nothing.

Common Stream API use case:

```java
names.forEach(name -> System.out.println(name));
```

---

### Interview Summary

> `Predicate` is used for condition checking and returns a boolean. `Function` is used for data transformation and returns a result. `Consumer` is used for performing actions and does not return anything. These functional interfaces are part of `java.util.function` and are commonly used with lambda expressions and Stream APIs.

This **What → Why → How** structure is usually what interviewers expect because it demonstrates not only that you know the definition but also when and how to use it.
