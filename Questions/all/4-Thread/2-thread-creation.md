# Que- What are the different ways to create a thread in Java?

### **Ways to Create a Thread in Java (Interview Expectation Answer – 5 YOE level)**

In Java, there are mainly **3 standard ways to create a thread**, and in real enterprise systems we prefer higher-level abstractions like Executor framework.

---

## **1. Extending the Thread class**

We create a class that extends `Thread` and override the `run()` method.

### **Example:**

```java
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread running: " + Thread.currentThread().getName());
    }
}

public class Test {
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        t1.start();
    }
}
```

### **Key points:**

* Simple to use
* Not preferred in real projects
* Not flexible (because Java does not support multiple inheritance)

---

## **2. Implementing Runnable interface (Preferred approach)**

We implement `Runnable` and pass it to a `Thread`.

### **Example:**

```java
class MyTask implements Runnable {
    @Override
    public void run() {
        System.out.println("Running in thread: " + Thread.currentThread().getName());
    }
}

public class Test {
    public static void main(String[] args) {
        Thread t1 = new Thread(new MyTask());
        t1.start();
    }
}
```

### **Key points:**

* Preferred over Thread class
* Supports better design (separation of task and thread)
* Can extend another class (since Runnable is interface)

---

## **3. Using Lambda Expression (Java 8+)**

Since `Runnable` is a functional interface, we can use lambda.

### **Example:**

```java
public class Test {
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            System.out.println("Thread running: " + Thread.currentThread().getName());
        });

        t1.start();
    }
}
```

### **Key points:**

* Clean and concise
* Widely used in modern Java applications

---

## **4. Using Executor Framework (MOST IMPORTANT for real-world systems)**

In enterprise applications (Spring Boot, microservices), we **do NOT manually create threads**.

We use:

### **ExecutorService example:**

```java
ExecutorService executor = Executors.newFixedThreadPool(5);

executor.submit(() -> {
    System.out.println("Task executed by: " + Thread.currentThread().getName());
});

executor.shutdown();
```

### **Key points interviewer expects:**

* Thread pool management
* Better performance and scalability
* Avoids thread creation overhead
* Used in production systems

---

## **Strong Interview Closing Statement**

“In real-world applications, especially Spring Boot microservices, we prefer Executor framework or CompletableFuture over manual thread creation because it provides better resource management, scalability, and control over concurrency.”

---

