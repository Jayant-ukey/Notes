# Que: Suppose you have a list of employees and need to filter employees whose age is greater than 60. How would you do this using Lambda Expressions or Streams?

### 1. Direct Answer

You can use Java Streams with a lambda expression to filter employees whose age is greater than 60 using the `filter()` method.

---

### 2. Why it is used

Streams with lambda expressions make the code **clean, readable, and declarative**. Instead of writing loops, you express *what you want* (filter condition) rather than *how to do it*.

---

### 3. How it works / used in practice

* Convert the list into a stream using `list.stream()`
* Apply `filter()` with a lambda condition (`emp -> emp.getAge() > 60`)
* Collect the result back into a list using `Collectors.toList()`

This processes data in a functional style and can also be parallelized if needed.

---

### 4. Real-world Java/Spring Boot example

```java
List<Employee> filteredEmployees = employees.stream()
        .filter(emp -> emp.getAge() > 60)
        .collect(Collectors.toList());
```

**Employee class example:**

```java
class Employee {
    private String name;
    private int age;

    public int getAge() {
        return age;
    }
}
```

---

### 5. Final Interview Answer (20–30 seconds)

We can filter employees whose age is greater than 60 using Java Streams and Lambda expressions. I would convert the employee list into a stream, apply the `filter()` method with a lambda condition like `emp -> emp.getAge() > 60`, and then collect the result back into a list using `Collectors.toList()`. This approach is concise, readable, and follows a functional programming style, making the code cleaner compared to traditional loops.
