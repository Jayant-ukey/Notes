# Que - How do you manage load balancing?

### ✅ Interview-Ready Answer (How I Manage Load Balancing in Microservices)

In a microservices architecture, load balancing is essential to ensure that incoming traffic is efficiently distributed across multiple instances of a service so that no single instance becomes a bottleneck. In my experience with Spring Boot-based microservices, I handle load balancing at multiple layers: **client-side, server-side, and infrastructure level**.

---

# 🔹 1. Client-Side Load Balancing (Spring Cloud LoadBalancer / Eureka)

When using Spring Cloud with Eureka:

* Each microservice registers itself with **Eureka Server**
* Service instances are discovered dynamically
* **Spring Cloud LoadBalancer** distributes requests across available instances

### 👉 Example:

If Order Service has 3 instances:

* Request 1 → Instance A
* Request 2 → Instance B
* Request 3 → Instance C

✔ Default strategy: **Round Robin**

👉 In older systems, Netflix Ribbon was used, but now Spring Cloud LoadBalancer is preferred.

---

# 🔹 2. API Gateway Load Balancing (Most Common in Real Systems)

In most production systems, **API Gateway plays a key role**:

* All client requests first hit the API Gateway
* Gateway routes requests to multiple instances of downstream services
* Works with service discovery (Eureka / Kubernetes DNS)

### ✔ Benefits:

* Centralized traffic control
* Security + routing + load balancing together
* Reduces complexity at client side

---

# 🔹 3. Infrastructure-Level Load Balancing (Kubernetes / Cloud)

In modern deployments:

### 👉 Kubernetes:

* Uses **Service + Ingress Controller**
* Distributes traffic across pods automatically
* Supports auto-scaling with HPA

### 👉 Cloud Load Balancers (AWS/GCP/Azure):

* ALB (Application Load Balancer)
* NLB (Network Load Balancer)

✔ This ensures high availability across zones/regions.

---

# 🔹 4. Load Balancing Strategies Used

Depending on system needs, different algorithms are used:

### ✔ Round Robin (default)

* Even distribution
* Simple and widely used

### ✔ Least Connection

* Sends traffic to least busy instance
* Good for variable workloads

### ✔ Weighted Load Balancing

* More powerful instances get more traffic

### ✔ Sticky Sessions (rare in microservices)

* Used only when session affinity is required (not preferred in microservices)

---

# 🔹 5. Resilience Along With Load Balancing

Load balancing alone is not enough, so I combine it with:

* **Circuit Breaker (Resilience4j)** → prevents calling unhealthy instances
* **Retry mechanism** → handles transient failures
* **Timeout configuration** → avoids hanging requests

👉 This ensures traffic is routed only to healthy services.

---

# 🔹 6. Real Production Example

In an e-commerce system:

* API Gateway receives request `/orders`
* It checks Eureka for available Order Service instances
* Load balancer selects one instance
* If one instance fails → traffic automatically shifts to others

✔ This ensures high availability and zero downtime experience.

---

# 📌 Key Points Interviewers Look For

* Understanding of **client-side vs server-side load balancing**
* Role of **API Gateway**
* Eureka + Spring Cloud LoadBalancer usage
* Kubernetes/cloud-native load balancing awareness
* Knowledge of algorithms (round robin, least connection)
* Integration with resilience patterns

---

# ⚠️ Common Follow-up Questions

* What is the difference between Ribbon and Spring Cloud LoadBalancer?
* How does Kubernetes handle load balancing internally?
* What happens if a service instance goes down?
* What is sticky session and why is it avoided in microservices?
* How does API Gateway decide routing?
* Difference between load balancing and API Gateway?

---

# 🧾 Short Answer (40–50 seconds)

“In my microservices architecture, load balancing is handled at multiple levels. At the application level, we use Spring Cloud LoadBalancer with Eureka, where service instances are dynamically discovered and requests are distributed using algorithms like round robin. At the API Gateway level, all client requests are routed through a single entry point, which further balances traffic across multiple service instances. In production environments, we also use Kubernetes or cloud load balancers like AWS ALB to distribute traffic across pods or nodes. Additionally, we combine load balancing with resilience patterns like circuit breakers and retries to ensure fault tolerance and high availability.”

---

If you want next, I can ask a **very strong interview follow-up**:
👉 “What happens if all instances of a service are down—how does your system behave?”

