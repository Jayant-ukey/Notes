# Que - What are the steps to create microservices.

### ✅ Interview-Ready Answer (What I would say in an interview)

If I had to explain how I design and build microservices from scratch in a real-world Spring Boot system, I would follow a structured, domain-driven approach rather than just creating multiple Spring Boot applications.

---

### 🧭 1. Understand the Business Domain (Most Important Step)

First, I start with **domain understanding and requirements gathering**.

* I identify **business capabilities** (not technical layers)
* I apply **Domain-Driven Design (DDD)** principles
* I break the system into **bounded contexts**

👉 Example:
For an e-commerce system:

* User Service (auth, profile)
* Product Service (catalog, inventory)
* Order Service (order processing)
* Payment Service (transactions)

✔ Interviewers expect: You don’t start coding immediately—you design around business domains.

---

### 🏗️ 2. Define Microservices Boundaries

Once domains are clear, I define:

* Service responsibilities (single responsibility per service)
* Data ownership (each service owns its own database)
* Service communication patterns (sync vs async)

✔ Key principle:
👉 “Database per service” (no shared DB)

---

### ⚙️ 3. Choose Tech Stack & Architecture Components

For a typical Spring Boot microservices system:

* Spring Boot (core framework)
* Spring Web (REST APIs)
* Spring Data JPA
* Spring Cloud components:

  * Eureka (Service Discovery)
  * API Gateway (Spring Cloud Gateway)
  * Config Server (centralized config)
* Messaging (Kafka / RabbitMQ) for async communication

---

### 🧩 4. Design Communication Between Services

Two types:

#### 🔹 Synchronous (REST/gRPC)

* Used for real-time data fetch
* Example: Order Service calling User Service

#### 🔹 Asynchronous (Messaging)

* Used for decoupling and scalability
* Example: Order placed → event sent to Kafka → Payment service consumes it

✔ Interview insight:
I prefer async where possible to avoid tight coupling.

---

### 🗄️ 5. Database Design per Service

* Each microservice has its own DB (PostgreSQL/MySQL/MongoDB)
* No direct DB sharing
* If data sharing is needed → use APIs/events

---

### 🔐 6. Implement Cross-Cutting Concerns

In production systems, I handle:

* Security → OAuth2 / JWT
* Logging → centralized logging (ELK stack)
* Monitoring → Prometheus + Grafana
* Tracing → Zipkin / Sleuth
* Exception handling → global exception handler

---

### 🚀 7. Build and Develop Microservices

For each service:

* Create Spring Boot project
* Define Controller → Service → Repository layers
* Expose REST endpoints
* Add validations and exception handling

---

### 🌐 8. API Gateway Setup

* Single entry point for all clients
* Handles:

  * Routing
  * Authentication
  * Rate limiting

Example:
Client → API Gateway → Microservices

---

### 🔍 9. Service Discovery & Config Management

* Eureka Server for dynamic service registration
* Config Server for centralized configurations

---

### 📦 10. Containerization & Deployment

* Dockerize each service
* Deploy using:

  * Kubernetes (preferred in large systems)
  * or Docker Compose (for small setups)

---

### 📊 11. Testing Strategy

* Unit Testing (JUnit + Mockito)
* Integration Testing (TestContainers)
* Contract Testing (Spring Cloud Contract)

---

### 📌 Key Points Interviewers Look For

* Understanding of **DDD and bounded contexts**
* “Database per service” principle
* Sync vs async communication trade-offs
* Knowledge of Spring Cloud ecosystem
* Production concerns (security, monitoring, scalability)

---

### ⚠️ Common Follow-up Questions

* How do you handle distributed transactions?
* What is Saga Pattern?
* How does API Gateway work internally?
* What happens if one microservice is down?
* How do you ensure data consistency across services?
* Eureka vs Consul?

---

### 🧾 Short Answer (40–50 seconds)

“To build microservices, I first start by identifying business domains using Domain-Driven Design and split the system into independent bounded contexts like user, order, and payment services. Each microservice is developed as an independent Spring Boot application with its own database to ensure loose coupling. Then I define communication between services using REST for synchronous calls and messaging like Kafka for asynchronous communication. I typically use Spring Cloud components like Eureka for service discovery and API Gateway for routing. Finally, I handle cross-cutting concerns like security, logging, monitoring, and deploy services using Docker and Kubernetes. The main goal is to build loosely coupled, independently deployable, and scalable services.”

---

Now send your next question 👍
