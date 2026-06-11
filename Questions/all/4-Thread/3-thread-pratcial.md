# Que: Can you show me how to create a thread by extending the Thread class and then start that thread?

Yes.

From an interview perspective, they expect a **simple correct example + understanding of start vs run**.

---

## **Creating a Thread by extending Thread class**

### **Step 1: Create a class that extends Thread**

We override the `run()` method — this contains the logic that will be executed in the new thread.

```java
class MyThread extends Thread {

    @Override
    public void run() {
        System.out.println("Thread is running: " + Thread.currentThread().getName());
    }
}
```

---

### **Step 2: Create object and start the thread**

```java
public class Test {
    public static void main(String[] args) {

        MyThread t1 = new MyThread();

        // Starts a new thread (internally calls run() on a separate call stack)
        t1.start();
    }
}
```

---

## **Important Interview Point (very critical)**

### ❌ If you call `run()` directly:

```java
t1.run();
```

* It will NOT create a new thread
* It runs like a normal method in main thread

### ✅ Correct way:

```java
t1.start();
```

* JVM creates a new thread
* Then internally calls `run()` method

---

## **What interviewer is looking for in your answer**

You should clearly mention:

* `run()` → contains task logic
* `start()` → creates new thread and invokes `run()`
* Difference between calling `start()` vs `run()`
* Thread runs independently from main thread

---

## **One-liner strong summary**

“When we extend the Thread class, we override the run method and call start() to execute it in a separate thread; calling run directly does not create a new thread.”

