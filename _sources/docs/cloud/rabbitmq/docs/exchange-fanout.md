# Fanout Exchange

![Image](https://www.tutlane.com/images/rabbitmq/rabbitmq_fanout_exchange_process_flow_diagram.PNG)

---

## 1. What is a Fanout Exchange?

A **Fanout Exchange** in RabbitMQ broadcasts every message it receives to **all queues bound to it**, **ignoring routing keys completely**.

Think of it as a **publish–subscribe (pub/sub)** model:

- Producer publishes a message to the exchange
- Exchange copies the message to **every bound queue**
- Each consumer receives **its own copy**

---

## 2. Key Characteristics

| Feature          | Fanout Exchange                         |
| ---------------- | --------------------------------------- |
| Routing Key      | ❌ Ignored                              |
| Message Delivery | To **all bound queues**                 |
| Binding Logic    | Simple binding (no condition)           |
| Pattern          | Publish–Subscribe                       |
| Use Case         | Notifications, events, logs, broadcasts |

---

## 3. How Fanout Exchange Works (Internals)

1. Producer sends message → **Fanout Exchange**
2. Exchange checks all queues bound to it
3. Message is **replicated** to each queue
4. Consumers read independently

> ⚠️ If **no queues are bound**, the message is **lost** (unless alternate exchange is configured)

---

## 4. Common Real-World Use Cases

- 🔔 System-wide notifications
- 📢 Event broadcasting (user registered, order placed)
- 🧾 Log aggregation
- 📊 Cache invalidation events
- 🧩 Microservice event distribution

---

## 5. Fanout vs Other Exchanges (Quick Comparison)

| Exchange   | Routing Logic            |
| ---------- | ------------------------ |
| Direct     | Exact routing key match  |
| Topic      | Pattern-based (`*`, `#`) |
| Headers    | Header attributes        |
| **Fanout** | Broadcast to all queues  |

---

# Spring Boot + RabbitMQ (Fanout Exchange)

We’ll build:

- 1 Fanout Exchange
- 2 Queues
- 2 Consumers
- 1 Producer

---

## 6. Maven Dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

---

## 7. Application Configuration (`application.yml`)

```yaml
spring:
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest
```

---

## 8. RabbitMQ Configuration (Fanout Exchange)

```java
@Configuration
public class RabbitMQConfig {

    public static final String FANOUT_EXCHANGE = "revs.fanout.exchange";
    public static final String QUEUE_EMAIL = "email.queue";
    public static final String QUEUE_SMS = "sms.queue";

    @Bean
    FanoutExchange fanoutExchange() {
        return new FanoutExchange(FANOUT_EXCHANGE);
    }

    @Bean
    Queue emailQueue() {
        return new Queue(QUEUE_EMAIL, true);
    }

    @Bean
    Queue smsQueue() {
        return new Queue(QUEUE_SMS, true);
    }

    @Bean
    Binding emailBinding(Queue emailQueue, FanoutExchange fanoutExchange) {
        return BindingBuilder.bind(emailQueue).to(fanoutExchange);
    }

    @Bean
    Binding smsBinding(Queue smsQueue, FanoutExchange fanoutExchange) {
        return BindingBuilder.bind(smsQueue).to(fanoutExchange);
    }
}
```

✅ **No routing key** is specified — because fanout ignores it.

---

## 9. Message Producer (Publisher)

```java
@Service
public class NotificationProducer {

    private final RabbitTemplate rabbitTemplate;

    public NotificationProducer(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    public void sendNotification(String message) {
        rabbitTemplate.convertAndSend(
                RabbitMQConfig.FANOUT_EXCHANGE,
                "", // routing key ignored
                message
        );
        System.out.println("Published: " + message);
    }
}
```

---

## 10. REST Controller to Trigger Publish

```java
@RestController
@RequestMapping("/notify")
public class NotificationController {

    private final NotificationProducer producer;

    public NotificationController(NotificationProducer producer) {
        this.producer = producer;
    }

    @PostMapping
    public String send(@RequestParam String msg) {
        producer.sendNotification(msg);
        return "Notification sent!";
    }
}
```

---

## 11. Consumer 1 – Email Service

```java
@Component
public class EmailConsumer {

    @RabbitListener(queues = RabbitMQConfig.QUEUE_EMAIL)
    public void consume(String message) {
        System.out.println("📧 Email Service received: " + message);
    }
}
```

---

## 12. Consumer 2 – SMS Service

```java
@Component
public class SmsConsumer {

    @RabbitListener(queues = RabbitMQConfig.QUEUE_SMS)
    public void consume(String message) {
        System.out.println("📱 SMS Service received: " + message);
    }
}
```

---

## 13. Execution Flow

```text
POST /notify?msg=User Registered

Fanout Exchange
     |
     |--> email.queue --> EmailConsumer
     |
     |--> sms.queue   --> SmsConsumer
```

Both consumers receive **the same message**.

---

## 14. Durable Fanout Exchange (Production Tip)

```java
new FanoutExchange("revs.fanout.exchange", true, false);
```

- `durable = true` → survives broker restart
- `autoDelete = false`

---

## 15. Common Mistakes ⚠️

❌ Expecting routing key filtering
❌ Forgetting to bind queues
❌ Publishing before queues are created
❌ Using fanout when message should go to only one consumer

---

## 16. When NOT to Use Fanout

- Load balancing (use **Work Queues**)
- Conditional routing (use **Direct / Topic**)
- Header-based filtering (use **Headers Exchange**)

---

## 17. Summary

- **Fanout Exchange = Broadcast**
- Routing keys are ignored
- Perfect for **event-driven microservices**
- Simple configuration
- Excellent for **notifications & pub/sub**

---
