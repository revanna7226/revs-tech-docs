# Headers Exchange

![Image](https://www.tutlane.com/images/rabbitmq/rabbitmq_headers_exchange_process_flow_diagram.PNG)

A **Headers Exchange** routes messages **based on message headers**, **not routing keys**.

👉 Instead of:

```
Exchange → routingKey → Queue
```

It uses:

```
Exchange → headers (key/value pairs) → Queue
```

---

## 2️⃣ Why Headers Exchange Exists (When to Use It)

Use **Headers Exchange** when:

- Routing depends on **multiple attributes**
- Routing logic is **dynamic**
- Routing key becomes **too complex**
- You want **SQL-like filtering**

### Example use cases

| Use Case                | Why Headers Exchange                |
| ----------------------- | ----------------------------------- |
| Multi-tenant systems    | Route by `tenant`, `region`, `plan` |
| Feature flags           | `feature=beta`, `userType=premium`  |
| Compliance routing      | `country=IN`, `gdpr=true`           |
| Microservices filtering | Avoid long routing keys             |

---

## 3️⃣ Key Concepts

### 🔹 1. Headers

Headers are **key-value pairs** inside message properties.

```java
headers:
{
  "type": "order",
  "priority": "high"
}
```

---

### 🔹 2. Binding Arguments

When binding a queue to a headers exchange, you define:

```text
x-match = all | any
```

| Value | Meaning                        |
| ----- | ------------------------------ |
| `all` | All headers must match         |
| `any` | At least one header must match |

---

## 4️⃣ How Routing Works

### Exchange

```
headers-exchange
```

### Bindings

| Queue | Binding                        |
| ----- | ------------------------------ |
| Q1    | `type=order AND priority=high` |
| Q2    | `type=order OR priority=low`   |

---

### Message Routing Example

| Message Headers             | Routed To |
| --------------------------- | --------- |
| `type=order, priority=high` | Q1, Q2    |
| `type=order, priority=low`  | Q2        |
| `type=payment`              | ❌ none   |

---

## 5️⃣ Comparison with Other Exchanges

| Exchange Type | Routing Based On     |
| ------------- | -------------------- |
| Direct        | Exact routing key    |
| Topic         | Wildcard routing key |
| Fanout        | Broadcast            |
| **Headers**   | Message headers      |

---

## 6️⃣ RabbitMQ Headers Exchange – Diagram (Conceptual)

![Image](https://jstobigdata.com/wp-content/uploads/2020/03/Headers_exchange-min.png)

![Image](https://www.cloudamqp.com/img/blog/rabbitmq-headers-exchange.svg)

---

## 7️⃣ Spring Boot Implementation (Complete)

### 📦 Dependencies (`pom.xml`)

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

---

## 8️⃣ Configuration – Exchange, Queues & Bindings

### 📁 `RabbitMQConfig.java`

```java
@Configuration
public class RabbitMQConfig {

    public static final String HEADERS_EXCHANGE = "headers-exchange";

    public static final String QUEUE_ALL = "queue-all-match";
    public static final String QUEUE_ANY = "queue-any-match";

    // Headers Exchange
    @Bean
    public HeadersExchange headersExchange() {
        return new HeadersExchange(HEADERS_EXCHANGE);
    }

    // Queues
    @Bean
    public Queue allMatchQueue() {
        return new Queue(QUEUE_ALL);
    }

    @Bean
    public Queue anyMatchQueue() {
        return new Queue(QUEUE_ANY);
    }

    // Binding (x-match = all)
    @Bean
    public Binding allMatchBinding() {
        Map<String, Object> headers = new HashMap<>();
        headers.put("type", "order");
        headers.put("priority", "high");
        headers.put("x-match", "all");

        return BindingBuilder
                .bind(allMatchQueue())
                .to(headersExchange())
                .whereAll(headers)
                .match();
    }

    // Binding (x-match = any)
    @Bean
    public Binding anyMatchBinding() {
        Map<String, Object> headers = new HashMap<>();
        headers.put("type", "order");
        headers.put("priority", "low");
        headers.put("x-match", "any");

        return BindingBuilder
                .bind(anyMatchQueue())
                .to(headersExchange())
                .whereAny(headers)
                .match();
    }
}
```

---

## 9️⃣ Producer – Sending Messages with Headers

### 📁 `MessageProducer.java`

```java
@Service
public class MessageProducer {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void sendMessage(String message, Map<String, Object> headers) {

        MessageProperties properties = new MessageProperties();
        headers.forEach(properties::setHeader);

        Message msg = new Message(
                message.getBytes(StandardCharsets.UTF_8),
                properties
        );

        rabbitTemplate.send(
                RabbitMQConfig.HEADERS_EXCHANGE,
                "", // routing key ignored
                msg
        );
    }
}
```

---

### 📁 Test Controller

```java
@RestController
@RequestMapping("/publish")
public class MessageController {

    @Autowired
    private MessageProducer producer;

    @GetMapping("/high")
    public String sendHighPriority() {
        Map<String, Object> headers = Map.of(
                "type", "order",
                "priority", "high"
        );

        producer.sendMessage("High priority order", headers);
        return "Message Sent";
    }

    @GetMapping("/low")
    public String sendLowPriority() {
        Map<String, Object> headers = Map.of(
                "type", "order",
                "priority", "low"
        );

        producer.sendMessage("Low priority order", headers);
        return "Message Sent";
    }
}
```

---

## 🔟 Consumers

### 📁 `AllMatchConsumer.java`

```java
@RabbitListener(queues = RabbitMQConfig.QUEUE_ALL)
public void consumeAllMatch(String message) {
    System.out.println("ALL MATCH QUEUE: " + message);
}
```

---

### 📁 `AnyMatchConsumer.java`

```java
@RabbitListener(queues = RabbitMQConfig.QUEUE_ANY)
public void consumeAnyMatch(String message) {
    System.out.println("ANY MATCH QUEUE: " + message);
}
```

---

## 1️⃣1️⃣ Key Observations (Very Important)

### ❗ Routing Key is Ignored

```java
rabbitTemplate.send(exchange, "", message);
```

---

### ❗ Headers Are Case-Sensitive

```text
priority ≠ Priority
```

---

### ❗ Performance

Headers exchange is:

- ❌ Slower than Direct/Topic
- ✅ Powerful for complex routing

Use **sparingly**.

---

## 1️⃣2️⃣ Real-World Pattern

### Alternative to Headers Exchange

Many teams prefer:

```
Topic Exchange
order.high.*
```

But headers exchange shines when:

- You don’t control routing key format
- You need **dynamic matching**
- You want **multi-dimensional routing**

---

## 1️⃣3️⃣ Summary

| Feature           | Headers Exchange |
| ----------------- | ---------------- |
| Routing based on  | Headers          |
| Supports AND / OR | ✅               |
| Routing key       | ❌ Ignored       |
| Performance       | ⚠ Slower         |
| Use case          | Complex rules    |

---
