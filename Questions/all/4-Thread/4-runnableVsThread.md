# Que: Can you tell me when we should use Runnable and when we should use Thread?

This is a very common interview question, and they expect a **clear comparison + real-world preference**.

---

## **When to use Runnable vs Thread (Interview Answer – 5 YOE level)**

### ✅ **1. Prefer Runnable (most of the time)**

We use `Runnable` when we want to define a **task** independently of the thread execution.

### **Why Runnable is preferred:**

* Separates **task (logic)** from **thread (execution)**
* Allows extending another class (since Java supports only single inheritance)
* Better design and reusability
* Works well with **Executor framework (industry standard)**

### **Example use case:**

* Processing a file
* Sending email asynchronously
* Background tasks in Spring Boot using `@Async`

```java id="1lh0ph"
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Task executed by: " + Thread.currentThread().getName());
    }
}
```

---

## **2. Use Thread class (rare in real projects)**

We extend `Thread` when:

* We want a **quick and simple thread implementation**
* We do NOT need to extend any other class
* Mostly used for **learning or small POC/demo code**

### **Limitation:**

* Breaks design flexibility
* Tightly couples task with thread
* Not suitable for enterprise applications

```java id="9ps3z8"
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread running");
    }
}
```

---

## **Key Interview Comparison Table**

| Feature                    | Runnable                 | Thread                      |
| -------------------------- | ------------------------ | --------------------------- |
| Type                       | Interface                | Class                       |
| Inheritance                | Can extend other classes | Cannot extend another class |
| Design                     | Better (task separation) | Tight coupling              |
| Reusability                | High                     | Low                         |
| Usage in real systems      | Preferred                | Rare                        |
| Executor framework support | Yes (recommended)        | Not preferred               |

---

## **Real-world Spring Boot perspective**

In enterprise applications:

* We use **Runnable / Callable + ExecutorService**
* Or Spring’s:

  * `@Async`
  * Thread pools
* We almost never extend Thread directly

---

## **Strong Interview Closing Statement**

“In real-world backend systems like Spring Boot microservices, we prefer Runnable because it provides better design flexibility and works well with thread pools and Executor framework. Extending Thread is generally avoided except for very simple use cases or learning purposes.”

---

