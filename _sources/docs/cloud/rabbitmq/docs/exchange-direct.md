# Direct Exchange

![Image](https://www.tutlane.com/images/rabbitmq/rabbitmq_direct_exchange_process_flow_diagram.PNG)

---

## 1. What is a Direct Exchange?

A **Direct Exchange** routes messages to queues **based on an exact match between the message’s routing key and the queue’s binding key**.

> **Rule:** > `routingKey == bindingKey` → message is delivered
> otherwise → message is ignored (or dead-lettered if configured)

### Key Characteristics

- **Exchange type:** `direct`
- **Routing:** Exact match
- **Use case:** Deterministic routing (command-based, severity-based, event-type-based)
- **One-to-one or one-to-many:**

  - One routing key → one queue
  - Same binding key → multiple queues (fan-out-like but controlled)

---

## 2. Core Concepts Refresher (Context)

| Concept     | Description                                |
| ----------- | ------------------------------------------ |
| Exchange    | Receives messages from producers           |
| Queue       | Stores messages for consumers              |
| Routing Key | String set by producer                     |
| Binding Key | String used when binding queue to exchange |

---

## 3. How Direct Exchange Works (Flow)

1. Producer sends message to **Direct Exchange** with a routing key
2. Exchange compares routing key with all bindings
3. If **exact match found**, message goes to that queue
4. If **no match**, message is dropped (unless DLX is configured)

---

## 4. Example Use Cases

### Common Patterns

1. **Log Level Routing**

   - `info` → infoQueue
   - `error` → errorQueue

2. **Command Processing**

   - `order.create`
   - `order.cancel`

3. **Microservice Event Routing**

   - `payment.success`
   - `payment.failed`

---

## 5. Direct Exchange vs Others (Quick Comparison)

| Exchange | Routing Logic            |
| -------- | ------------------------ |
| Direct   | Exact match              |
| Fanout   | Broadcast                |
| Topic    | Pattern match (`*`, `#`) |
| Headers  | Header attributes        |

---

# Spring Boot + RabbitMQ (Direct Exchange)

Below is a **production-style setup** using Spring Boot + Spring AMQP.

---

## 6. Maven Dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

---

## 7. Application Properties

```properties
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
```

---

## 8. RabbitMQ Configuration (Direct Exchange)

### Exchange, Queues, Bindings

```java
@Configuration
public class RabbitMQConfig {

    public static final String DIRECT_EXCHANGE = "order.direct.exchange";

    public static final String ORDER_CREATE_QUEUE = "order.create.queue";
    public static final String ORDER_CANCEL_QUEUE = "order.cancel.queue";

    public static final String ORDER_CREATE_KEY = "order.create";
    public static final String ORDER_CANCEL_KEY = "order.cancel";

    @Bean
    public DirectExchange directExchange() {
        return new DirectExchange(DIRECT_EXCHANGE);
    }

    @Bean
    public Queue orderCreateQueue() {
        return new Queue(ORDER_CREATE_QUEUE, true);
    }

    @Bean
    public Queue orderCancelQueue() {
        return new Queue(ORDER_CANCEL_QUEUE, true);
    }

    @Bean
    public Binding createBinding() {
        return BindingBuilder
                .bind(orderCreateQueue())
                .to(directExchange())
                .with(ORDER_CREATE_KEY);
    }

    @Bean
    public Binding cancelBinding() {
        return BindingBuilder
                .bind(orderCancelQueue())
                .to(directExchange())
                .with(ORDER_CANCEL_KEY);
    }
}
```

---

## 9. Message Producer (Publisher)

```java
@Service
public class OrderProducer {

    private final RabbitTemplate rabbitTemplate;

    public OrderProducer(RabbitTemplate rabbitTemplate) {
        this.rabbitTemplate = rabbitTemplate;
    }

    public void sendCreateOrder(String message) {
        rabbitTemplate.convertAndSend(
                RabbitMQConfig.DIRECT_EXCHANGE,
                RabbitMQConfig.ORDER_CREATE_KEY,
                message
        );
    }

    public void sendCancelOrder(String message) {
        rabbitTemplate.convertAndSend(
                RabbitMQConfig.DIRECT_EXCHANGE,
                RabbitMQConfig.ORDER_CANCEL_KEY,
                message
        );
    }
}
```

---

## 10. Message Consumers (Listeners)

### Create Order Consumer

```java
@Service
public class OrderCreateConsumer {

    @RabbitListener(queues = RabbitMQConfig.ORDER_CREATE_QUEUE)
    public void handleCreateOrder(String message) {
        System.out.println("Create Order Received: " + message);
    }
}
```

### Cancel Order Consumer

```java
@Service
public class OrderCancelConsumer {

    @RabbitListener(queues = RabbitMQConfig.ORDER_CANCEL_QUEUE)
    public void handleCancelOrder(String message) {
        System.out.println("Cancel Order Received: " + message);
    }
}
```

---

## 11. REST Controller to Test

```java
@RestController
@RequestMapping("/orders")
public class OrderController {

    private final OrderProducer producer;

    public OrderController(OrderProducer producer) {
        this.producer = producer;
    }

    @PostMapping("/create")
    public String createOrder() {
        producer.sendCreateOrder("Order Created Successfully");
        return "Create Order Message Sent";
    }

    @PostMapping("/cancel")
    public String cancelOrder() {
        producer.sendCancelOrder("Order Cancelled Successfully");
        return "Cancel Order Message Sent";
    }
}
```

---

## 12. What Happens at Runtime?

| Routing Key    | Delivered To            |
| -------------- | ----------------------- |
| `order.create` | order.create.queue      |
| `order.cancel` | order.cancel.queue      |
| `order.update` | ❌ dropped (no binding) |

---

## 13. Important Notes (Interview + Production)

### ✔ Best Practices

- Use **Direct Exchange** for **command-style routing**
- Use **durable queues** for reliability
- Configure **DLQ (Dead Letter Exchange)** for unmatched or failed messages
- Keep routing keys **explicit and meaningful**

### ❌ Common Mistakes

- Expecting wildcard routing (use **Topic Exchange** instead)
- Forgetting bindings
- Typos in routing keys

---

## 14. When Should You Use Direct Exchange?

✅ Use Direct Exchange when:

- You need **exact routing**
- Message type is known
- Clear producer → consumer contract exists

❌ Avoid when:

- You need pattern-based routing
- You want broadcast behavior
