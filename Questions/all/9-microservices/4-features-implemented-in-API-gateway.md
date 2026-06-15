# Que - You mentioned API Gateway. What are the features that you have implemented in the API Gateway?

### ✅ Interview-Ready Answer (API Gateway Features I Implemented)

In my microservices architecture, the API Gateway is a critical component and I typically use **Spring Cloud Gateway** as a single entry point for all client requests. It is not just routing traffic—it also handles multiple cross-cutting concerns centrally.

---

## 🔹 1. Request Routing (Core Feature)

The primary responsibility is routing requests to appropriate microservices.

* Based on URL paths or predicates
* Example:

  * `/users/**` → User Service
  * `/orders/**` → Order Service

✔ This avoids exposing internal service URLs to clients.

---

## 🔹 2. Authentication & Authorization (JWT Security)

One of the most important features I implemented:

* Gateway validates **JWT tokens**
* Extracts user roles and permissions
* Rejects unauthorized requests at the edge itself

✔ Benefit:

* Prevents unnecessary load on downstream services
* Centralized security enforcement

---

## 🔹 3. Request Filtering (Pre & Post Filters)

I implemented custom filters for:

### Pre-filters:

* Logging incoming requests
* Adding correlation ID (for tracing)
* Validating headers or tokens

### Post-filters:

* Response logging
* Modifying response headers if needed

---

## 🔹 4. Rate Limiting & Throttling

To protect services from abuse:

* Implemented rate limiting using Redis-based token bucket algorithm
* Example:

  * 100 requests/min per user or IP

✔ Prevents system overload and ensures fair usage

---

## 🔹 5. Centralized Logging & Correlation ID

* Each request is assigned a **correlation ID**
* Passed through all microservices
* Helps trace request flow across distributed systems

✔ Very useful in debugging production issues

---

## 🔹 6. Load Balancing

* Gateway works with **Eureka service discovery**
* Automatically routes requests to healthy instances
* Supports client-side load balancing

---

## 🔹 7. Retry & Fault Handling (Basic Level)

* Configured retry policies for transient failures
* Integrated with resilience patterns (where applicable)

---

## 🔹 8. CORS Configuration

* Configured CORS at gateway level
* Allowed specific origins (frontend applications)
* Controlled allowed headers and methods

---

## 🔹 9. SSL Termination (In Production Setup)

* TLS/SSL handled at gateway level
* Internal services communicate over internal network without HTTPS overhead

---

## 🔹 10. API Versioning Support

* Routed based on versioning:

  * `/v1/users`
  * `/v2/users`

✔ Helps in backward compatibility during deployments

---

## 🧠 Production Insights (What Interviewers Like)

* Gateway is not just routing—it is a **policy enforcement layer**
* It reduces load on microservices by handling:

  * auth
  * rate limiting
  * logging
* It becomes the **single entry point for observability + security**

---

## 📌 Key Points Interviewers Look For

* JWT validation at gateway vs service level
* Filters (pre/post) in Spring Cloud Gateway
* Rate limiting implementation (Redis/token bucket)
* Correlation ID and distributed tracing
* Eureka integration for dynamic routing
* Security boundary role of API Gateway

---

## ⚠️ Common Follow-up Questions

* Why do we need API Gateway instead of direct service calls?
* Difference between Zuul and Spring Cloud Gateway?
* Where should authentication happen—gateway or service?
* How does rate limiting work internally?
* What happens if API Gateway goes down?
* How do you handle high availability of gateway?

---

## 🧾 Short Answer (40–50 seconds)

“In my microservices architecture, the API Gateway acts as the single entry point for all client requests. I have implemented features like request routing to different services, JWT-based authentication and authorization, and custom pre and post filters for logging and request validation. I also implemented rate limiting using Redis to protect services from overload and added correlation IDs for distributed tracing across microservices. Additionally, it integrates with Eureka for dynamic service discovery and handles CORS configuration and API versioning. Overall, the gateway acts as a centralized layer for security, routing, monitoring, and traffic control.”

---

If you want next, I can also give you:
👉 “JWT flow in API Gateway + Spring Security (very frequently asked)”
