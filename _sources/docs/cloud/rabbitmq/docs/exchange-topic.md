# Topic Exchange

![alt text](https://www.tutlane.com/images/rabbitmq/rabbitmq_topic_exchange_process_flow_diagram.PNG)

A **Topic Exchange** routes messages to queues **based on routing-key patterns**.

- Producers send messages with a **routing key**
- Queues are bound with **patterns**
- RabbitMQ delivers the message to **all queues whose binding pattern matches the routing key**

👉 This makes Topic Exchange ideal for:

- Event-driven systems
- Log processing
- Microservices communication
- Selective message consumption

---

## 2️⃣ How Topic Exchange Routing Works

Routing keys are **dot-separated words**:

```
order.created.us
user.updated.profile
payment.failed.card
```

### Wildcards Used

| Wildcard | Meaning                        |
| -------- | ------------------------------ |
| `*`      | Matches **exactly one word**   |
| `#`      | Matches **zero or more words** |

### Examples

| Binding Pattern | Matches Routing Keys                   |
| --------------- | -------------------------------------- |
| `order.*`       | ❌ `order.created.us` (too many words) |
| `order.*.*`     | ✅ `order.created.us`                  |
| `order.#`       | ✅ `order.created.us`                  |
| `*.created.*`   | ✅ `order.created.us`                  |
| `#.failed`      | ✅ `payment.failed`                    |

---

## 3️⃣ Visual Flow (Topic Exchange)

---

## 4️⃣ Topic Exchange vs Others (Quick Comparison)

| Exchange Type | Routing Logic    |
| ------------- | ---------------- |
| Direct        | Exact match      |
| Fanout        | Broadcast to all |
| **Topic**     | Pattern-based    |
| Headers       | Header matching  |

---

## 5️⃣ Spring Boot – Topic Exchange Implementation

We’ll build:

- **Topic Exchange**
- **Multiple Queues**
- **Bindings with patterns**
- **Producer**
- **Consumers**

---

## 6️⃣ Maven Dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

---

## 7️⃣ application.yml

```yaml
spring:
  rabbitmq:
    host: localhost
    port: 5672
    username: guest
    password: guest
```

---

## 8️⃣ RabbitMQ Configuration (Topic Exchange)

```java
@Configuration
public class RabbitMQTopicConfig {

    public static final String TOPIC_EXCHANGE = "order-topic-exchange";

    public static final String ORDER_QUEUE = "order-queue";
    public static final String PAYMENT_QUEUE = "payment-queue";
    public static final String ALL_EVENTS_QUEUE = "all-events-queue";

    @Bean
    TopicExchange topicExchange() {
        return new TopicExchange(TOPIC_EXCHANGE);
    }

    @Bean
    Queue orderQueue() {
        return new Queue(ORDER_QUEUE, true);
    }

    @Bean
    Queue paymentQueue() {
        return new Queue(PAYMENT_QUEUE, true);
    }

    @Bean
    Queue allEventsQueue() {
        return new Queue(ALL_EVENTS_QUEUE, true);
    }

    // order.*.*
    @Bean
    Binding orderBinding() {
        return BindingBuilder
                .bind(orderQueue())
                .to(topicExchange())
                .with("order.*.*");
    }

    // payment.#
    @Bean
    Binding paymentBinding() {
        return BindingBuilder
                .bind(paymentQueue())
                .to(topicExchange())
                .with("payment.#");
    }

    // #
    @Bean
    Binding allEventsBinding() {
        return BindingBuilder
                .bind(allEventsQueue())
                .to(topicExchange())
                .with("#");
    }
}
```

---

## 9️⃣ Message Producer (Publisher)

```java
@Service
public class OrderEventPublisher {

    private final RabbitTemplate rabbitTemplate;

    public OrderEventPublisher(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    public void sendEvent(String routingKey, Object message) {
        rabbitTemplate.convertAndSend(
                RabbitMQTopicConfig.TOPIC_EXCHANGE,
                routingKey,
                message
        );
    }
}
```

### Example Usage

```java
publisher.sendEvent("order.created.us", "Order created in US");
publisher.sendEvent("payment.failed.card", "Payment failed");
publisher.sendEvent("user.updated.profile", "User updated profile");
```

---

## 🔟 Consumers (Listeners)

### Order Events Consumer

```java
@Component
public class OrderConsumer {

    @RabbitListener(queues = RabbitMQTopicConfig.ORDER_QUEUE)
    public void consumeOrderEvents(String message) {
        System.out.println("Order Service received: " + message);
    }
}
```

### Payment Events Consumer

```java
@Component
public class PaymentConsumer {

    @RabbitListener(queues = RabbitMQTopicConfig.PAYMENT_QUEUE)
    public void consumePaymentEvents(String message) {
        System.out.println("Payment Service received: " + message);
    }
}
```

### All Events Consumer (Audit / Logging)

```java
@Component
public class AuditConsumer {

    @RabbitListener(queues = RabbitMQTopicConfig.ALL_EVENTS_QUEUE)
    public void consumeAllEvents(String message) {
        System.out.println("Audit Service received: " + message);
    }
}
```

---

## 1️⃣1️⃣ Message Routing Result

| Routing Key            | Order Queue | Payment Queue | All Events |
| ---------------------- | ----------- | ------------- | ---------- |
| `order.created.us`     | ✅          | ❌            | ✅         |
| `payment.failed.card`  | ❌          | ✅            | ✅         |
| `user.updated.profile` | ❌          | ❌            | ✅         |

---

## 1️⃣2️⃣ Real-World Use Cases

✔ Microservices event communication
✔ Multi-region event routing
✔ Log aggregation
✔ Audit & monitoring systems
✔ E-commerce order/payment workflows

---

## 1️⃣3️⃣ Best Practices

- Use **meaningful routing key structure** (`domain.event.region`)
- Avoid too many wildcards on producers
- Keep **exchange durable**
- Use **DLQ + Retry** for failures
- Prefer **Topic Exchange** for evolving systems

---
