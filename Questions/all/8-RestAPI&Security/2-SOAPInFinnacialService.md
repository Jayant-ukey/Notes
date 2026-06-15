# Que - Why do some financial services still prefer SOAP over REST?


## ✅ Interview-ready answer

Some financial services still prefer **SOAP over REST** mainly because SOAP provides **strong standards, built-in security, strict contracts, and enterprise-level reliability**, which are critical in banking and financial systems where correctness, compliance, and transaction safety matter more than simplicity or speed of development.

---

## 📌 How I explain it in an interview

Even though REST is widely used in modern microservices, SOAP is still preferred in many financial systems because it offers **enterprise-grade features out of the box**, especially around **security, formal contracts, and reliability guarantees**.

---

# 🔐 1. Strong built-in security (key reason)

SOAP supports **WS-Security**, which provides:

* Message-level encryption
* Digital signatures
* End-to-end security (not just transport-level)

👉 This is critical in banking where messages may pass through multiple systems.

REST usually relies on:

* HTTPS
* OAuth2 / JWT

👉 REST security is good, but SOAP is more standardized for enterprise security compliance.

---

# 📜 2. Strict contract using WSDL

SOAP uses **WSDL (Web Service Description Language)** which defines:

* Exact request/response structure
* Data types
* Service operations

👉 This ensures **strong type safety and zero ambiguity between systems**.

In banking systems:

* Multiple systems (legacy + modern) must interact correctly
* Even small mismatches can cause financial inconsistencies

---

# ⚙️ 3. Formal standards and governance

SOAP is governed by strict standards:

* WS-Security
* WS-ReliableMessaging
* WS-AtomicTransaction

👉 These standards ensure:

* Transaction integrity
* Message reliability
* Consistency across distributed systems

---

# 🔄 4. Reliable messaging and ACID-like behavior

SOAP supports:

* Guaranteed delivery
* Acknowledgement mechanisms
* Retry policies at protocol level

👉 Important for:

* Money transfers
* Payment processing
* Banking transactions

---

# 🧱 5. Better for legacy and enterprise systems

Many financial institutions:

* Have legacy systems built on SOAP
* Use middleware like ESBs (Enterprise Service Bus)
* Already have WSDL-based integrations

👉 Migrating everything to REST is expensive and risky.

---

# 🧠 6. Transactional integrity

SOAP supports **distributed transactions (WS-AtomicTransaction)**

👉 Useful in scenarios like:

* Fund transfer between accounts
* Multi-step financial operations

REST does not have built-in transaction standards.

---

# 📊 Quick comparison (context of finance)

| Feature      | SOAP                          | REST                     |
| ------------ | ----------------------------- | ------------------------ |
| Security     | WS-Security (strong)          | HTTPS + OAuth            |
| Contract     | WSDL (strict)                 | Flexible/OpenAPI         |
| Reliability  | Built-in messaging guarantees | App-level handling       |
| Transactions | Supported (WS-* standards)    | Not built-in             |
| Suitability  | Banking/enterprise systems    | Web/mobile/microservices |

---

# ⭐ Key points interviewers look for

* SOAP is still used due to **security + strict contracts**
* WS-Security is a major advantage in finance
* WSDL ensures strong schema validation
* Supports enterprise-level reliability standards
* Legacy systems heavily depend on SOAP
* REST is more modern but less strict

---

# 🚀 Production-level insights

* Many banks use **hybrid architecture**:

  * SOAP for core banking systems
  * REST for mobile apps and APIs
* SOAP services are often exposed via **API gateways or ESBs**
* Migration from SOAP to REST is slow due to compliance risks
* Financial systems prioritize:

  * correctness > performance
  * consistency > flexibility

---

# 🔁 Common follow-up questions

1. What is WS-Security in SOAP?
2. What is WSDL and why is it important?
3. Why is REST more popular in microservices?
4. Can REST achieve the same reliability as SOAP?
5. What is ACID transaction vs REST API behavior?
6. What is an ESB in enterprise architecture?

---

If you want next, I can give you a **real senior-level interview follow-up:**
👉 *“Can REST achieve the same level of security and reliability as SOAP? If yes, how?”*
