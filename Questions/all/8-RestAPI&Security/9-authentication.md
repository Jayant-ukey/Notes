# Que - What is Authentication?

## ✅ Interview-ready answer

**Authentication** is the process of verifying the identity of a user or system. It answers the question:

👉 **“Who are you?”**

In a Spring Boot or web application context, authentication ensures that only legitimate users can access the system by validating their credentials like username/password, tokens, or certificates.

---

## 📌 How I explain it in an interview

When a user tries to access an application, authentication is the first step where the system checks whether the user is valid by verifying their credentials against a trusted source (like a database, LDAP, or identity provider).

If the credentials are correct, the user is authenticated and allowed to proceed.

---

## 🧱 Example flow

1. User enters username/password
2. Request goes to server
3. Server validates credentials
4. If valid → authentication successful
5. If invalid → access denied (401 Unauthorized)

---

## 🔐 Example in real systems

* Login with username/password
* OAuth login (Google, GitHub)
* JWT token validation in microservices

---

## 📊 Authentication vs Authorization (important distinction)

| Concept        | Meaning            | Question answered |
| -------------- | ------------------ | ----------------- |
| Authentication | Verifies identity  | Who are you?      |
| Authorization  | Checks permissions | What can you do?  |

---

## 🧠 Key points interviewers look for

* Authentication = identity verification
* Happens before authorization
* Common methods:

  * Username/password
  * JWT tokens
  * OAuth2 / OpenID Connect
* Fails usually return **401 Unauthorized**

---

## 🚀 Production-level insights (Spring Boot context)

* Spring Security handles authentication
* Common implementations:

  * Form login authentication
  * JWT-based stateless authentication (microservices)
  * OAuth2 for third-party login
* Passwords must always be **hashed (BCrypt)**, never stored in plain text
* Authentication should be stateless in microservices for scalability

---

## 🔁 Common follow-up questions

1. What is the difference between authentication and authorization?
2. What is JWT and how does it work?
3. What is Spring Security?
4. What is Basic Authentication?
5. What is OAuth2 and where is it used?
6. What HTTP status code is used for authentication failure?

---

If you want next, I can give you a **very common interview deep question:**
👉 *“How does JWT-based authentication work in Spring Boot microservices?”*
