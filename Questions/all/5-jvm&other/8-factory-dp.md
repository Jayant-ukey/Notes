# Que - What is the difference between Factory and Abstract Factory design patterns?

### 1. Direct Answer (What)

The **Factory Pattern** and **Abstract Factory Pattern** are both creational design patterns, but they differ in scope:

* **Factory Pattern**: Used to create **one type of object** based on input, without exposing instantiation logic.
* **Abstract Factory Pattern**: Used to create **families of related objects** (multiple types of objects that belong together) without specifying their concrete classes.

In short:

> Factory = creates one product
> Abstract Factory = creates a family of related products

---

### 2. Internal Understanding (How)

#### 🔹 Factory Pattern (Simple Factory / Factory Method)

It delegates object creation to a method.

Example: Payment method selection

```java id="f1a1"
interface Payment {
    void pay();
}
```

```java id="f1a2"
class UpiPayment implements Payment {
    public void pay() {}
}

class CardPayment implements Payment {
    public void pay() {}
}
```

Factory:

```java id="f1a3"
class PaymentFactory {
    public static Payment getPayment(String type) {
        if (type.equals("UPI")) return new UpiPayment();
        else if (type.equals("CARD")) return new CardPayment();
        return null;
    }
}
```

👉 One factory, one product hierarchy.

---

#### 🔹 Abstract Factory Pattern

It provides an interface to create **multiple related objects**.

Example: UI components for different OS

We may need:

* Button
* Checkbox

for each OS.

```java id="a2b1"
interface Button {
    void render();
}

interface Checkbox {
    void render();
}
```

Concrete implementations:

```java id="a2b2"
class WindowsButton implements Button {
    public void render() {}
}

class MacButton implements Button {
    public void render() {}
}
```

Factory interface:

```java id="a2b3"
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}
```

Concrete factories:

```java id="a2b4"
class WindowsFactory implements GUIFactory {
    public Button createButton() { return new WindowsButton(); }
    public Checkbox createCheckbox() { return new WindowsCheckbox(); }
}
```

👉 One factory produces a **family of related objects**.

---

### 3. Real-world Experience (Spring Boot Context)

#### Factory Pattern in Spring:

* `BeanFactory`
* `ApplicationContext`
* Custom service factories (e.g., payment gateways, notification services)

Use case:

* Selecting payment strategy dynamically

---

#### Abstract Factory Pattern in real systems:

* UI component libraries (theoretical in Java desktop apps)
* Cross-platform SDKs
* Cloud provider abstractions (AWS vs Azure services)
* In Spring:

  * Environment-specific bean creation (`@Profile`)
  * Configuration-based service families

Example:

* AWS S3 storage service vs Azure Blob storage service

Both include:

* StorageService
* AuthService
* LoggingService

👉 Abstract Factory ensures consistent family selection.

---

### 4. Key Differences (Interview-Friendly)

| Feature         | Factory Pattern       | Abstract Factory Pattern     |
| --------------- | --------------------- | ---------------------------- |
| Purpose         | Creates one product   | Creates family of products   |
| Complexity      | Simple                | More complex                 |
| Object creation | One hierarchy         | Multiple related hierarchies |
| Extension       | Add new product types | Add new families             |
| Example         | PaymentFactory        | UIFactory (Windows/Mac)      |

---

### 5. Best Practices / Production Considerations

* Use **Factory Pattern** when object creation depends on simple conditions
* Use **Abstract Factory** when multiple related objects must be created together
* Avoid over-engineering—don’t introduce Abstract Factory unless needed
* In Spring Boot, often DI + configuration replaces manual factories
* Prefer interface-based design for flexibility

---

### 6. Key Points Interviewers Look For

* Clear distinction: **one product vs family of products**
* Ability to give real-world examples
* Understanding of use cases in Spring Boot
* Awareness of complexity difference
* Mention of scalability and maintainability
* Practical reasoning (not just definitions)

---

### 7. Common Follow-up Questions

1. Can you combine Factory and Strategy patterns?
2. Where does Spring use Factory Pattern internally?
3. What problem does Abstract Factory solve that Factory cannot?
4. Is Abstract Factory always necessary in microservices?
5. Difference between Factory Method and Abstract Factory?
6. Can Dependency Injection replace Factory Pattern?
7. Real-world example from your project?

---

### One-Line Senior-Level Summary

> "Factory Pattern is used to create a single type of object based on input conditions, whereas Abstract Factory Pattern is used when we need to create families of related objects in a consistent way, ensuring that all related objects belong to the same context or configuration."
