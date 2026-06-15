# Que - What will be the status code when you try to delete a resource from the server?

## ✅ Interview-ready answer

When a resource is successfully deleted from the server, the standard HTTP status codes used are:

### ✔ **200 OK**

* Returned when the server successfully deletes the resource and also returns a response body (e.g., confirmation message or deleted resource details).

### ✔ **204 No Content (most preferred in REST APIs)**

* Returned when the delete operation is successful but there is **no response body**.
* This is the most commonly used and REST-best-practice status code for DELETE operations.

---

## 📌 How I explain it in an interview

In most RESTful APIs, after successfully deleting a resource, I prefer returning **204 No Content** because the operation is successful and there is nothing more to return in the response body.

---

## 🧱 Example in Spring Boot

```java id="d1"
@DeleteMapping("/employees/{id}")
public ResponseEntity<Void> deleteEmployee(@PathVariable Long id) {
    service.deleteEmployee(id);
    return ResponseEntity.noContent().build();
}
```

---

## 📊 Summary of status codes for DELETE

| Status Code    | Meaning                                 | When to use                |
| -------------- | --------------------------------------- | -------------------------- |
| 200 OK         | Deletion successful with response body  | If you return message/data |
| 202 Accepted   | Deletion accepted but not completed yet | Async deletion             |
| 204 No Content | Deletion successful, no response body   | ⭐ Best practice            |

---

## ⭐ Key points interviewers look for

* DELETE success → typically `204 No Content`
* `200 OK` is also valid but less preferred for REST purity
* `202 Accepted` used for async operations
* REST best practice prefers minimal response for DELETE

---

## 🚀 Production-level insights

* Most modern REST APIs use **204 to keep responses lightweight**
* In microservices, DELETE may be:

  * synchronous → 204
  * asynchronous (Kafka/event-driven) → 202
* Always ensure idempotency in DELETE operations
* Proper logging is important since response body is often empty

---

## 🔁 Common follow-up questions

1. What is the difference between 200 and 204 status codes?
2. Can DELETE return a response body?
3. What is 202 Accepted used for?
4. Is DELETE idempotent in REST?
5. What happens if resource does not exist (status code)?
6. What is the correct status code for “not found during delete”?

---

If you want next, I can give you a **tricky interview scenario:**
👉 *“What status code should be returned if the resource to delete does not exist?”*
