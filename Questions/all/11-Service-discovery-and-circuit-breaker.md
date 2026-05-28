# 1. Service Discovery

## What is it?

In microservices, services are **dynamic**:

* Instances can scale up/down
* IP addresses change frequently
* Services are deployed in containers/Kubernetes

So we cannot hardcode service locations.

👉 **Service Discovery solves this problem by maintaining a registry of all available service instances.**

---

## Example Problem (Without Service Discovery)

```text id="d7h2kq"
Order Service → calls Payment Service at 192.168.1.10
```

If Payment Service restarts → IP changes → system breaks ❌

---

## Solution

Instead of hardcoding:

```text id="xq7m0z"
Order Service → Service Registry → Payment Service instance
```

---

## Types of Service Discovery

### 1. Client-side discovery

Client fetches service instances and chooses one.

### 2. Server-side discovery

Client calls a load balancer, which routes request.

---

## In Spring Ecosystem

We use:

* Spring Cloud Netflix
* Spring Cloud LoadBalancer

---

# 2. Eureka (Service Registry)

## What is Eureka?

Netflix Eureka is a **service registry** used in microservices.

It is part of Spring Cloud Netflix.

---

## Role of Eureka

Eureka has two parts:

### 1. Eureka Server

* Central registry
* Stores service metadata

### 2. Eureka Client

* Microservices register themselves
* Also discover other services

---

## How Eureka Works

### Step 1: Registration

```text id="k8j3xv"
Payment Service → registers itself in Eureka
```

### Step 2: Heartbeat

Services send periodic heartbeats to stay alive.

### Step 3: Discovery

```text id="h4n9qp"
Order Service → asks Eureka → Payment Service instance list
```

---

## Architecture Flow

```text id="m2z9lk"
        +-------------------+
        |   Eureka Server   |
        +-------------------+
          ↑      ↑      ↑
   Service A  Service B  Service C
```

---

## Key Features of Eureka

* Service registration
* Health monitoring
* Load balancing support
* Failover handling

---

## Spring Boot Setup (Conceptual)

### Server

```java id="e7xq0p"
@EnableEurekaServer
@SpringBootApplication
public class EurekaServerApplication {}
```

---

### Client

```java id="n5kq2r"
@EnableEurekaClient
@SpringBootApplication
public class OrderServiceApplication {}
```

---

# 3. Circuit Breaker Pattern

## What is Circuit Breaker?

In distributed systems:

* Services fail
* Network issues happen
* Latency increases

👉 Circuit breaker prevents **cascading failures**

---

## Real-world Example

```text id="v8m2qx"
Order Service → Payment Service (DOWN)
```

Without circuit breaker:

* Requests keep failing
* Threads get blocked
* System crashes

---

## With Circuit Breaker:

```text id="p3n7kd"
Order Service → Circuit Breaker → Payment Service
                      ↓
               fallback response
```

---

# Circuit Breaker States

## 1. CLOSED (Normal)

* Requests pass through
* Monitoring failures

---

## 2. OPEN (Failure state)

* Requests are blocked
* Fallback is executed immediately

---

## 3. HALF-OPEN

* Test if service is back
* If successful → go to CLOSED
* If failure → back to OPEN

---

## State Diagram

```text id="b9q0lk"
CLOSED → OPEN → HALF-OPEN → CLOSED
```

---

# Why Circuit Breaker is Needed?

* Prevent system overload
* Avoid cascading failures
* Improve system resilience
* Provide fallback responses

---

# Spring Implementation

We use:

* Resilience4j

---

## Example

```java id="c3m9qp"
@CircuitBreaker(name = "paymentService", fallbackMethod = "fallback")
public String callPaymentService() {
    return restTemplate.getForObject(url, String.class);
}

public String fallback(Exception e) {
    return "Payment Service is currently unavailable";
}
```

---

## Other Resilience Patterns

* Retry
* Rate limiter
* Bulkhead
* Time limiter

---

# Real Microservices Flow (Putting it together)

```text id="z1x7qp"
Client
  ↓
API Gateway
  ↓
Order Service
  ↓
Eureka (service lookup)
  ↓
Payment Service
  ↓
Circuit Breaker monitors call
```

---

# Real Project Explanation (Very Important)

You can say:

> “In our microservices architecture, we used Eureka Server as a service registry where all services like Order, Payment, and Inventory registered themselves. Services discovered each other dynamically using Eureka instead of hardcoded URLs. For resilience, we used Resilience4j circuit breaker to handle failures. If a dependent service like Payment Service was down, fallback methods were triggered to prevent cascading failures.”

---

# Common Interview Questions

## Why do we need service discovery?

Because service instances are dynamic in cloud environments.

---

## Eureka vs Kubernetes service discovery?

* Eureka → application-level registry
* Kubernetes → infrastructure-level DNS-based discovery

---

## What happens when Eureka is down?

* Existing services continue working
* But new service discovery may fail
* Cached registry is used temporarily

---

## Why circuit breaker instead of retry only?

Retry alone can:

* Increase load
* Worsen failure
  Circuit breaker stops traffic completely when service is unhealthy.

---

# Short Crisp Interview Answer

> Service Discovery is used in microservices to dynamically locate service instances instead of hardcoding URLs.
>
> Eureka is a service registry where services register themselves and discover other services dynamically.
>
> Circuit Breaker is a design pattern used to prevent cascading failures in distributed systems. It has three states: CLOSED, OPEN, and HALF-OPEN. If a service is failing, requests are blocked and fallback methods are executed.
>
> In Spring Boot, Eureka is used for service discovery and Resilience4j is used for implementing circuit breaker for fault tolerance and system resilience.
