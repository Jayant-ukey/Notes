# Explain Generic in Java


## 1️⃣ Definition

**Generics in Java allow classes, interfaces, and methods to operate on different data types while providing type safety at compile time.**

They were introduced in **Java 5** to eliminate type casting and make code more reusable and safer.

---

## 2️⃣ Why Generics Are Needed

Before generics, collections stored objects as `Object`, so we had to cast them.

### Without Generics

```java
List list = new ArrayList();
list.add("Hello");

String s = (String) list.get(0); // Explicit casting required
```

Problems:

* No **compile-time type checking**
* **ClassCastException** possible at runtime
* Lots of **manual casting**

---

### With Generics

```java
List<String> list = new ArrayList<>();
list.add("Hello");

String s = list.get(0); // No casting needed
```

Benefits:

* ✔ Compile-time type safety
* ✔ No explicit casting
* ✔ Cleaner and more readable code
* ✔ Reusable components

---

## 3️⃣ Generic Class Example

A class that works with any type.

```java
class Box<T> {
    private T value;

    public void setValue(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}
```

Usage:

```java
Box<Integer> intBox = new Box<>();
intBox.setValue(10);

Box<String> strBox = new Box<>();
strBox.setValue("Hello");
```

`T` represents a **type parameter**.

---

## 4️⃣ Generic Method Example

```java
public <T> void printValue(T value) {
    System.out.println(value);
}
```

Usage:

```java
printValue(10);
printValue("Java");
```

---

## 5️⃣ Bounded Generics

You can **restrict the type** a generic accepts.

Example:

```java
class Calculator<T extends Number> {
    T number;
}
```

Here `T` can only be:

* Integer
* Double
* Float
* etc. (anything extending `Number`)

---

## 6️⃣ Wildcards in Generics

Used mainly in collections.

Example:

```java
List<?> list;
```

Types of wildcards:

| Type            | Meaning       |
| --------------- | ------------- |
| `<?>`           | Unknown type  |
| `<? extends T>` | Upper bounded |
| `<? super T>`   | Lower bounded |

Example:

```java
public void printNumbers(List<? extends Number> list) {
    for(Number n : list){
        System.out.println(n);
    }
}
```

---

## 7️⃣ Important Concept (Often Expected)

**Type Erasure**

Java implements generics using **type erasure**, meaning generic type information is removed at runtime.

Example:

```java
List<String>
List<Integer>
```

Both become:

```java
List
```

at runtime.

---

## 8️⃣ Short Interview Answer (30-second version)

If interviewer wants a **quick answer**, say:

> Generics in Java allow classes, interfaces, and methods to work with different data types while maintaining type safety at compile time. They were introduced in Java 5 and are mainly used with collections to avoid casting and prevent runtime `ClassCastException`. Generics improve code reusability and readability. They use type parameters like `<T>` and are implemented internally using type erasure.

