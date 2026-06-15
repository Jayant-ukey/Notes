# Que - Is it 201 or 204 for "No Content"?

## ❌ Quick correction (important interview clarity)

It is **NOT 201 for “No Content”**.

👉 The correct status code for **No Content is 204**.

---

## 📌 Clear explanation

### ✔ **204 No Content**

* Used when the request is successful
* But there is **no response body**
* Commonly used for **DELETE operations**

```http
204 No Content
```

Example:

```java
return ResponseEntity.noContent().build();
```

---

### ❌ **201 Created**

* Used when a **new resource is successfully created**
* Typically used in **POST operations**

```http
201 Created
```

Example:

```java
@PostMapping("/employees")
public ResponseEntity<Employee> createEmployee(@RequestBody Employee emp) {
    Employee saved = service.save(emp);
    return ResponseEntity.status(HttpStatus.CREATED).body(saved);
}
```

---

## 📊 Simple comparison

| Status Code        | Meaning                       | Use Case               |
| ------------------ | ----------------------------- | ---------------------- |
| **201 Created**    | Resource created successfully | POST                   |
| **204 No Content** | Success, no response body     | DELETE / sometimes PUT |

---

## 🎯 Interview-ready clarification

If the interviewer asks:

👉 *“Is it 201 or 204 for no content?”*

You should answer:

> “204 No Content is used when the operation is successful but there is no response body, typically in DELETE operations.
> 201 Created is used when a new resource is created successfully, usually in POST requests.”

---

## ⭐ Key interview takeaway

* **201 = Creation**
* **204 = No content after success**
* They serve completely different purposes and are not interchangeable

---

## 🔁 Common follow-up questions

1. Can DELETE return 200 instead of 204?
2. What is the difference between 200 and 204?
3. When should we use 201 vs 200 for POST?
4. Can PUT return 201?
5. What happens if DELETE is called on a non-existing resource?

---

If you want, I can next give you a **real tricky REST interview question:**
👉 *“Can a DELETE request return 201 Created? Why or why not?”*
