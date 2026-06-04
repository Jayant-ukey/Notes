## Que: Can an abstract class implement multiple interfcaes and also extend another abstract class simeltaneoulsy OR Can an Abstract Class implement multiple Interfaces and extend another Abstract Class simultaneously?


**Yes, absolutely.**

An abstract class can:

1. **Extend another abstract class** (single inheritance)
2. **Implement one or more interfaces** (multiple interfaces)

at the same time.

---

## Example

```java
interface Printable {
    void print();
}

interface Scannable {
    void scan();
}

abstract class Machine {
    abstract void start();
}

abstract class MultiFunctionPrinter
        extends Machine
        implements Printable, Scannable {

    @Override
    public void print() {
        System.out.println("Printing...");
    }
}
```

This is perfectly valid.

Here:

* `MultiFunctionPrinter` extends `Machine`
* Implements `Printable`
* Implements `Scannable`

Since it is still abstract, it doesn't need to implement all interface methods (`scan()` can remain unimplemented).

---

## Why Does This Work?

Java supports:

### Single Class Inheritance

```java
extends Machine
```

A class can extend only one class (abstract or concrete).

### Multiple Interface Inheritance

```java
implements Printable, Scannable
```

A class can implement multiple interfaces.

Combining both is allowed.

---

## Complete Example

```java
interface A {
    void methodA();
}

interface B {
    void methodB();
}

abstract class Parent {
    abstract void parentMethod();
}

abstract class Child
        extends Parent
        implements A, B {

    @Override
    public void methodA() {
        System.out.println("Method A");
    }
}
```

Valid because:

* Extends one abstract class
* Implements multiple interfaces
* Remains abstract, so it doesn't have to implement `methodB()` and `parentMethod()` yet

---

## Interview Follow-up

### Does an abstract class have to implement all interface methods?

**No.**

If the class itself is abstract, it may leave some or all interface methods unimplemented.

Example:

```java
interface Payment {
    void pay();
}

abstract class PaymentService implements Payment {
}
```

This compiles.

The concrete subclass must implement `pay()`.

---

## If the class is not abstract?

Then it **must implement all abstract methods** inherited from:

* Interfaces
* Abstract parent classes

Otherwise compilation fails.

---

## Interview Answer (30 seconds)

> Yes. An abstract class can extend one abstract class and implement multiple interfaces simultaneously. Java allows single inheritance for classes and multiple inheritance through interfaces. Since the class is abstract, it is not required to implement all inherited abstract methods immediately; those can be implemented by its concrete subclasses.
