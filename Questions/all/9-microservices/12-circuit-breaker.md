# Que - What is the Circuit Breaker pattern?

### ✅ Interview-Ready Answer (Circuit Breaker Pattern)

The **Circuit Breaker pattern** is a **resilience design pattern used in microservices** to prevent repeated calls to a failing service and to avoid cascading failures across the system.

In simple terms, it acts like an **electrical circuit breaker**—if a service is failing too often, it temporarily stops calls to that service and allows it time to recover.

---

# 🔹 1. Why Circuit Breaker is needed

In microservices, services communicate over the network, and failures are common due to:

* Network latency
* Service downtime
* Overloaded downstream services
* Database issues

👉 Without circuit breaker:

* One failing service keeps getting called
* Threads get blocked
* Resources get exhausted
* Entire system may go down (cascading failure)

✔ Circuit breaker prevents this situation.

---

# 🔹 2. How Circuit Breaker works (States)

Circuit breaker has **3 main states**:

---

## 🟢 Closed State (Normal Flow)

* Requests are allowed normally
* Calls go to the dependent service
* Failures are monitored

👉 If failure rate increases beyond threshold → switch to OPEN

---

## 🔴 Open State (Failure Protection Mode)

* All requests are blocked immediately
* No call is made to the failing service
* Fallback response is returned

👉 This protects system from wasting resources

---

## 🟡 Half-Open State (Recovery Testing)

* After a timeout period, a few test requests are allowed
* If they succeed → circuit closes again
* If they fail → circuit opens again

---

# 🔹 3. Example in Microservices (Real Scenario)

Let’s say:

* Order Service → calls Payment Service

If Payment Service is down:

### Without Circuit Breaker:

* Every request keeps hitting Payment Service
* Order Service threads get blocked
* System slows down or crashes

### With Circuit Breaker:

* After failures threshold reached:

  * Circuit opens
  * Order Service stops calling Payment Service
  * Returns fallback response like:

    * “Payment service is temporarily unavailable”

✔ System remains stable

---

# 🔹 4. Implementation in Spring Boot

In Spring Boot microservices, we use:

👉 **Resilience4j Circuit Breaker**

```java id="cb1"
@CircuitBreaker(name = "paymentService", fallbackMethod = "paymentFallback")
public String callPaymentService() {
    return restTemplate.getForObject("http://PAYMENT-SERVICE/pay", String.class);
}
```

### Fallback method:

```java id="cb2"
public String paymentFallback(Exception ex) {
    return "Payment service is currently unavailable. Please try later.";
}
```

---

# 🔹 5. Key Benefits

✔ Prevents cascading failures
✔ Improves system stability
✔ Avoids thread exhaustion
✔ Enables graceful degradation
✔ Improves user experience during partial outages

---

# 🔹 6. Production Best Practices

* Set proper failure thresholds (not too sensitive)
* Use time windows for monitoring failures
* Combine with:

  * Retry (for transient failures)
  * Timeout (to avoid long waits)
  * Bulkhead (for isolation)
* Always implement meaningful fallback logic
* Monitor circuit state changes using Prometheus/Grafana

---

# 📌 Key Points Interviewers Look For

* Understanding of **Closed / Open / Half-Open states**
* Real-world problem: cascading failures
* Difference between retry and circuit breaker
* Role of fallback method
* Why it is essential in microservices
* Awareness of Resilience4j

---

# ⚠️ Common Follow-up Questions

* What is cascading failure?
* Difference between retry and circuit breaker?
* When does circuit breaker go to half-open state?
* What is fallback method?
* How does system recover after circuit opens?
* What metrics control circuit breaker behavior?

---

# 🧾 Short Answer (40–50 seconds)

“The Circuit Breaker pattern is a resilience pattern used in microservices to prevent repeated calls to a failing service. It has three states: closed, where requests flow normally; open, where calls are blocked due to high failure rate; and half-open, where a few test requests are allowed to check recovery. If a service like Payment Service is down, the circuit breaker opens and prevents further calls, returning a fallback response instead. This helps avoid cascading failures, reduces load on failing services, and keeps the system stable. In Spring Boot, we implement it using Resilience4j along with fallback methods.”

---

If you want next, I can ask you a **very important senior-level follow-up**:
👉 “What is the difference between Circuit Breaker, Retry, and Timeout—and when do you use each?”
