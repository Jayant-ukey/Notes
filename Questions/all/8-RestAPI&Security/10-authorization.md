# Que -  What is Authorization?

## ✅ Interview-ready answer

**Authorization** is the process of determining what an authenticated user is allowed to do in a system. It answers the question:

👉 **“What are you allowed to access or perform?”**

So, after a user is authenticated (identity verified), authorization decides the user’s permissions based on roles, privileges, or policies.

---

## 📌 How I explain it in an interview

Once a user successfully logs in (authentication), the system checks their role or permissions before allowing access to specific resources or actions.

For example, an admin user may have access to all APIs, while a normal user may only access limited endpoints.

---

## 🧱 Example scenario

* User logs in successfully → authenticated
* User tries to access `/admin/dashboard`
* System checks role:

  * If role = ADMIN → allow access
  * If role = USER → deny access

---

## 🔐 Simple analogy

* Authentication = entering a building with an ID card
* Authorization = access control deciding which rooms you can enter

---

## 📊 Authentication vs Authorization

| Feature  | Authentication    | Authorization        |
| -------- | ----------------- | -------------------- |
| Meaning  | Verifies identity | Checks permissions   |
| Question | Who are you?      | What can you do?     |
| Happens  | First             | After authentication |
| Example  | Login             | Role-based access    |

---

## ⚙️ In Spring Boot (Spring Security context)

Authorization is typically implemented using:

* Role-based access control (RBAC)
* Method-level security

### Example:

```java id="a1"
@PreAuthorize("hasRole('ADMIN')")
@GetMapping("/admin")
public String adminAccess() {
    return "Admin content";
}
```

---

## 🚫 HTTP status code for authorization failure

* **403 Forbidden** → user is authenticated but not allowed

---

## 🧠 Key points interviewers look for

* Authorization happens after authentication
* It defines access control rules
* Based on roles/permissions/policies
* Commonly implemented using Spring Security
* Failure results in **403 Forbidden**

---

## 🚀 Production-level insights

* Use **RBAC (Role-Based Access Control)** in most enterprise systems
* For complex systems, use:

  * ABAC (Attribute-Based Access Control)
* JWT tokens often carry roles/claims for authorization
* Microservices use centralized auth systems (OAuth2 / Keycloak)
* Always enforce authorization at backend, not just frontend

---

## 🔁 Common follow-up questions

1. Difference between authentication and authorization?
2. What is RBAC?
3. What is 401 vs 403 status code?
4. How does Spring Security handle authorization?
5. What are roles and authorities in Spring Security?
6. Can a user be authenticated but not authorized?

---

If you want next, I can give you a **real senior-level interview question:**
👉 *“Can you explain how JWT helps in both authentication and authorization in microservices?”*
