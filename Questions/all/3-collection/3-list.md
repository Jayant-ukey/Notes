# Que: What is the difference between ArrayList and LinkedList?

### 1. Direct Answer

`ArrayList` is backed by a dynamic array, while `LinkedList` is based on a doubly linked list structure.

---

### 2. Why it is used

Both are used to store ordered collections, but the choice depends on **performance requirements** like frequent access vs frequent insertion/deletion.

---

### 3. How it works / used in practice

* **ArrayList**

  * Uses resizable array internally
  * Fast random access using index `O(1)`
  * Slow insertion/deletion in middle `O(n)` due to shifting elements

* **LinkedList**

  * Uses nodes (prev + data + next)
  * No shifting required for insertion/deletion `O(1)` (if position is known)
  * Slow random access `O(n)` because traversal is needed

---

### 4. Real-world Java/Spring Boot example

```java id="l9xv2a"
List<String> arrayList = new ArrayList<>();
arrayList.add("A");
arrayList.get(0); // fast access
```

```java id="m1kq8b"
List<String> linkedList = new LinkedList<>();
linkedList.addFirst("A");
linkedList.addLast("B");
```

**Spring Boot usage:**

* `ArrayList` → API response lists, database result sets (read-heavy)
* `LinkedList` → Queue-like operations, task processing systems

---

### 5. Final Interview Answer (20–30 seconds)

ArrayList is backed by a dynamic array and provides fast random access but slower insertions and deletions in the middle. LinkedList is implemented using a doubly linked list, which allows faster insertions and deletions but slower access because traversal is required. So, ArrayList is preferred for read-heavy operations, while LinkedList is preferred for frequent insertions and deletions.
