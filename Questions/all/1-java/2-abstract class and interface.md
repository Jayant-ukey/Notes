# Que - Diffenrce between abstract class and interface OR Difference between Abstract Class and Interface.
---

# Difference Between Abstract Class and Interface

Both are used to achieve abstraction, but they serve different purposes.

## Abstract Class

An abstract class represents an **"is-a" relationship** and is used when classes share common state and behavior.

```java
abstract class Vehicle {

    protected String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }

    abstract void start();

    public void stop() {
        System.out.println("Vehicle stopped");
    }
}
```

---

## Interface

An interface represents a **contract** that classes agree to implement.

```java
interface PaymentService {

    void processPayment();
}
```

```java
class CreditCardPayment implements PaymentService {

    @Override
    public void processPayment() {
        System.out.println("Processing payment");
    }
}
```

---

# Key Differences

| Feature               | Abstract Class                         | Interface                                      |
| --------------------- | -------------------------------------- | ---------------------------------------------- |
| Purpose               | Shared implementation + abstraction    | Contract/behavior definition                   |
| Constructors          | Yes                                    | No                                             |
| Instance Variables    | Allowed                                | Only constants (`public static final`)         |
| Method Implementation | Can have abstract and concrete methods | Can have abstract, default, and static methods |
| Multiple Inheritance  | Not supported                          | Supported                                      |
| Access Modifiers      | Any access modifier                    | Methods are public by default                  |
| State Management      | Can maintain state                     | Generally stateless                            |
| Keyword               | `extends`                              | `implements`                                   |

---

# Multiple Inheritance Example

Abstract class:

```java
abstract class A {}
abstract class B {}

// Not possible
class C extends A, B {}
```

Interface:

```java
interface A {}
interface B {}

class C implements A, B {}
```

This is one of the biggest advantages of interfaces.

---

# When Should You Use Abstract Class?

Use an abstract class when:

* Multiple classes share common code
* You want to provide default implementations
* You need constructors
* You need instance variables/state

### Example

```java
abstract class Employee {

    protected String employeeId;

    public void login() {
        System.out.println("Common login logic");
    }

    abstract void calculateSalary();
}
```

---

# When Should You Use Interface?

Use an interface when:

* You want to define a contract
* Unrelated classes need the same behavior
* Multiple inheritance is required
* You want loose coupling

### Example

```java
interface NotificationService {
    void sendNotification();
}
```

Implementations:

```java
class EmailNotification implements NotificationService {}
class SmsNotification implements NotificationService {}
class PushNotification implements NotificationService {}
```

---

# Spring Boot Real-World Example

This is what interviewers like to hear.

### Interface

```java
public interface PaymentService {
    void pay();
}
```

```java
@Service
public class UpiPaymentService implements PaymentService {
    public void pay() {}
}
```

```java
@Service
public class CardPaymentService implements PaymentService {
    public void pay() {}
}
```

Spring injects the implementation using Dependency Injection.

This follows:

* Interface-based programming
* Dependency Inversion Principle

---

# Common Follow-up Question

## Can an Interface Have Methods with Implementation?

Yes, since Java 8:

```java
interface Vehicle {

    default void start() {
        System.out.println("Starting...");
    }

    static void info() {
        System.out.println("Vehicle Interface");
    }
}
```

---

## Can an Abstract Class Have No Abstract Methods?

Yes.

```java
abstract class Employee {

    public void display() {
        System.out.println("Employee");
    }
}
```

It can still be declared abstract to prevent instantiation.

---

# Interview-Friendly Answer

> An abstract class is used when multiple related classes share common state and behavior. It can have constructors, instance variables, and both abstract and concrete methods.
>
> An interface is used to define a contract that multiple classes can implement. It supports multiple inheritance and promotes loose coupling.
>
> In Spring Boot projects, we generally prefer interfaces for service contracts and dependency injection, while abstract classes are used when we need shared implementation or common state across related classes.

---

# One-Line Answer

> Use an **abstract class** when you need shared state and common implementation; use an **interface** when you need a contract and want loose coupling with support for multiple inheritance.
