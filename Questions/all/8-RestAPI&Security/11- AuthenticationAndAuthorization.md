# Que- What is the difference between Authentication and Authorization?

## ✅ Interview-ready answer

**Authentication and Authorization are two fundamental security concepts, but they serve different purposes in an application.**

* **Authentication** verifies *who the user is*
* **Authorization** determines *what the user is allowed to do*

---

## 📌 How I explain it in an interview

First, the system confirms the user’s identity through authentication. Once the user is authenticated, the system checks their permissions or roles to decide what resources they can access — that is authorization.

---

## 🔐 Key difference

| Feature           | Authentication                       | Authorization        |
| ----------------- | ------------------------------------ | -------------------- |
| Meaning           | Identity verification                | Access control       |
| Question answered | Who are you?                         | What can you do?     |
| Purpose           | Login validation                     | Permission check     |
| Happens           | First                                | After authentication |
| Depends on        | Credentials (username/password, JWT) | Roles, permissions   |
| Failure code      | 401 Unauthorized                     | 403 Forbidden        |

---

## 🧱 Simple real-world example

* User logs in with username/password → **Authentication successful**
* Same user tries to access admin API:

  * If role = ADMIN → allowed
  * If role = USER → denied → **Authorization failure**

---

## 🏢 Simple analogy

* Authentication = Entering a building using an ID card
* Authorization = Accessing only allowed rooms inside the building

---

## ⚙️ Spring Boot context

### Authentication example:

* Login using JWT / form login
* System verifies credentials

### Authorization example:

```java id="z1"
@PreAuthorize("hasRole('ADMIN')")
@GetMapping("/admin")
public String adminPanel() {
    return "Admin Data";
}
```

---

## 🧠 Key points interviewers look for

* Authentication = identity verification
* Authorization = permission check
* Authentication happens before authorization
* Authentication failure → 401
* Authorization failure → 403
* Both are handled in Spring Security

---

## 🚀 Production-level insights

* In microservices:

  * Authentication is often handled by **OAuth2 / Keycloak / Auth server**
  * Authorization is done using **roles/claims in JWT**
* JWT token often contains:

  * user identity (authentication)
  * roles/permissions (authorization)
* Always enforce authorization on backend APIs, not just frontend UI
* Use RBAC for most enterprise systems

---

## 🔁 Common follow-up questions

1. What is 401 vs 403 in HTTP?
2. How does JWT support both authentication and authorization?
3. What is RBAC in Spring Security?
4. What is OAuth2?
5. Can a user be authenticated but not authorized?
6. Where is authentication handled in microservices architecture?

---

If you want next, I can give you a **very important senior-level question:**
👉 *“How does Spring Security handle authentication and authorization internally?”*
