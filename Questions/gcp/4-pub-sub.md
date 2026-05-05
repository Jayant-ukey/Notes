# Pub/Sub (Publish/Subscribe)

Pub/Sub (Publish/Subscribe) is an asynchronous messaging pattern used to decouple service producers from consumers, allowing them to communicate independently in distributed systems.

Publishers send messages to a topic without knowing who receives them, while subscribers receive only the messages they are interested in. This enables scalable, many-to-many communication.

---

## Key Concepts

- **Publishers:**  
  Services that create and send messages (events) to a topic.

- **Topics:**  
  Intermediary channels where messages are sent.

- **Subscribers:**  
  Services that receive messages from topics.

- **Asynchronous Communication:**  
  Producers do not wait for consumers to receive messages, increasing system flexibility and robustness.
