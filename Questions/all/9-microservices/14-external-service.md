# Que - If your API depends on an external service that occasionally fails, what would you do?

### ✅ Interview-Ready Answer (External Service Occasionally Failing)

If my API depends on an external service that occasionally fails, I design the system to be **resilient, fault-tolerant, and failure-aware**, rather than assuming the external service will always be available. In real Spring Boot microservices, I handle this using a combination of **timeout, retry, circuit breaker, and fallback strategies**, depending on the business criticality.

---

# 🔹 1. First Step: Set Strict Timeout

The first thing I ensure is that the call to the external service has a **hard timeout**.

* Prevents thread blocking
* Avoids resource exhaustion
* Ensures fast failure

👉 Example: If response is not received in 2 seconds → fail fast

✔ This is critical in production systems.

---

# 🔹 2. Retry Mechanism (For Temporary Failures Only)

If the failure is intermittent (network glitch, temporary downtime):

* I use **Retry with exponential backoff**
* Typically 2–3 retries max

👉 Important:
✔ Only for **idempotent operations** (GET, safe POST with idempotency key)

---

# 🔹 3. Circuit Breaker (To Stop Repeated Failures)

If the external service is frequently failing:

* Circuit breaker detects high failure rate
* It **opens the circuit**
* Stops further calls for a cooldown period

👉 This prevents:

* Wasting resources
* Cascading failures
* Increased latency

---

# 🔹 4. Fallback Strategy (Most Important for User Experience)

This is where I decide what to return to the client when the external service is unavailable.

### Common fallback approaches:

## ✔ Option 1: Cached Response (Preferred)

* Return last successful response from Redis/cache

## ✔ Option 2: Default Response

* Return a simplified or generic response

## ✔ Option 3: Graceful Degradation

* Disable non-critical features but keep API working

## ✔ Option 4: Controlled Error Response

* Return meaningful HTTP status like:

  * `503 Service Unavailable`

---

# 🔹 5. Asynchronous Alternative (Best Design in Many Cases)

If real-time response is not mandatory:

* Instead of calling external service synchronously
* I publish an event to Kafka/RabbitMQ

👉 External service processes it later

✔ This completely decouples dependency and avoids failures impacting user requests.

---

# 🔹 6. Monitoring & Observability

I also ensure:

* Metrics for external API latency & failures (Prometheus)
* Logs for debugging (ELK stack)
* Distributed tracing (Zipkin)

👉 This helps detect issues before they impact users.

---

# 🔹 7. Real Production Example

Example scenario:

* My Order Service calls a **third-party payment gateway API**

If it fails:

1. Timeout ensures quick failure
2. Retry attempts 2 times
3. Circuit breaker opens if failures continue
4. Fallback returns:

   * “Payment is pending, we will retry”
5. Background job retries payment later

✔ This ensures user experience is not blocked

---

# 📌 Key Points Interviewers Look For

* Timeout is the first line of defense
* Retry only for transient failures
* Circuit breaker prevents cascading failures
* Fallback ensures user experience is preserved
* Awareness of external dependency risk
* Async messaging as a better design alternative
* Production observability mindset

---

# ⚠️ Common Follow-up Questions

* How do you decide retry count?
* What is idempotency and why is it important here?
* What happens if fallback also fails?
* How do you handle third-party SLA violations?
* Why is async better than sync for external dependencies?
* How does circuit breaker recover?

---

# 🧾 Short Answer (40–50 seconds)

“If my API depends on an external service that occasionally fails, I first ensure proper timeouts so the request fails fast instead of blocking resources. Then I use retry with exponential backoff for transient failures, but only for idempotent operations. I also use a circuit breaker to stop repeated calls when the failure rate increases. For better user experience, I implement fallback mechanisms like returning cached data, default responses, or a 503 status. In some cases, I prefer asynchronous processing using Kafka to decouple the dependency completely. Additionally, I monitor external service health using logging, metrics, and tracing tools.”

---

If you want next, I can give you a **very senior-level follow-up question**:
👉 “How do you design a system when an external dependency has SLA issues and is frequently unreliable?”
