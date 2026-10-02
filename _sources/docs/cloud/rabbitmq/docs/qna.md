# Questions & Answers

## **SECTION 1: FUNDAMENTALS (Q1-Q5)**

### Q1. What is RabbitMQ and what problems does it solve?

**Answer:**
RabbitMQ is an open-source message broker that implements the AMQP (Advanced Message Queuing Protocol) standard. It enables applications to communicate asynchronously.

**Problems it solves:**

- **Decoupling**: Producer and consumer don't need to know each other
- **Asynchronous Processing**: Long-running tasks don't block the main application
- **Load Leveling**: Handle traffic spikes by queuing messages
- **Reliability**: Ensures messages are delivered even if components fail
- **Scalability**: Distribute messages across multiple consumers

**Example Scenario:**

```
Order Service (Producer) → RabbitMQ → Email Service (Consumer)
                        → SMS Service (Consumer)
                        → Inventory Service (Consumer)
```

Order service publishes once; multiple services consume independently.

---

### Q2. What is AMQP and how does it differ from other protocols?

**Answer:**
AMQP (Advanced Message Queuing Protocol) is a standardized, open protocol for reliable messaging.

**Comparison:**

| Feature              | AMQP                                 | HTTP         | MQTT        | Kafka              |
| -------------------- | ------------------------------------ | ------------ | ----------- | ------------------ |
| **Reliability**      | High (transactions, acknowledgments) | Medium       | Low         | High               |
| **Routing**          | Complex routing rules                | Simple       | Topic-based | Partition-based    |
| **Overhead**         | Medium                               | High         | Low         | Medium             |
| **Use Case**         | Enterprise messaging                 | Web requests | IoT sensors | Big data streaming |
| **RabbitMQ Support** | Native                               | Via plugins  | Via plugin  | No                 |

**Key AMQP Features:**

- Message acknowledgment (confirms delivery)
- Transactions support
- Complex routing patterns
- Standardized across brokers

---

### Q3. What is the difference between a Queue and a Topic in RabbitMQ?

**Answer:**
RabbitMQ doesn't have "queues vs topics" distinction like Kafka. Instead, it uses **Exchanges** and **Queues**.

**Queues:**

- Final destination for messages
- Consumed by applications
- Messages wait here until consumer processes
- Always 1-to-1 (queue → single message flow)

**Exchanges:**

- Receive messages from producers
- Route messages to queues
- Act as distribution hubs
- Support different routing patterns

**Example:**

```
Producer → Exchange (route based on routing key) → Queue → Consumer
```

**Different Exchange Types (similar to "topics"):**

- **Direct**: Exact match on routing key (like point-to-point)
- **Topic**: Pattern-based routing (like publish-subscribe)
- **Fanout**: Broadcast to all bound queues (like topic broadcast)
- **Headers**: Route by message headers

---

### Q4. What is the AMQP model architecture?

**Answer:**
AMQP defines a layered architecture:

```
┌─────────────────────────────────────────┐
│  Application Layer (Your Spring Code)  │
├─────────────────────────────────────────┤
│  AMQP Model Layer                       │
│  ├─ Exchanges                           │
│  ├─ Queues                              │
│  └─ Bindings                            │
├─────────────────────────────────────────┤
│  Functional Layer                       │
│  ├─ Basic (publish/consume)             │
│  ├─ Queue (declare/delete)              │
│  └─ Exchange (declare/delete)           │
├─────────────────────────────────────────┤
│  Transport Layer (TCP/SSL)              │
└─────────────────────────────────────────┘
```

**Key Components:**

- **Exchange**: Receives messages from publishers
- **Queue**: Holds messages for consumers
- **Binding**: Links exchange to queue with routing rules
- **Routing Key**: Message attribute for routing decisions

---

### Q5. What are Channels and Connections in RabbitMQ?

**Answer:**

**Connection:**

- TCP connection between client and RabbitMQ server
- Lightweight to establish
- One connection per application is typical
- Each connection consumes server resources

**Channel:**

- Logical connection within a single TCP connection
- Lightweight, multiplexed
- All communication (publish/consume) happens over channels
- Each channel is single-threaded

**Analogy:**

```
Connection = Physical telephone line
Channels = Multiple conversations on that line (via multiplexing)
```

**Java Example:**

```java
// One Connection
Connection connection = factory.newConnection();

// Multiple Channels on one Connection (efficient)
Channel channel1 = connection.createChannel();  // Publish
Channel channel2 = connection.createChannel();  // Consume
Channel channel3 = connection.createChannel();  // Consume

// Spring Boot handles this automatically
// But understanding helps with performance tuning
```

**Best Practice:**

- Use 1 connection per JVM
- Create multiple channels for concurrent operations
- Close channels and connections properly

---

## **SECTION 2: EXCHANGES & QUEUES (Q6-Q12)**

### Q6. Explain the different types of Exchanges in detail.

**Answer:**

**1. Direct Exchange**

- Routes messages based on exact routing key match
- Best for: Point-to-point communication

```
Producer publishes with routing_key = "order.created"
Exchange checks: Does any queue have this binding?
Yes → Queue A is bound with "order.created"
Message → Queue A (only)
```

```java
@Bean
public DirectExchange orderExchange() {
    return new DirectExchange("orders");
}

@Bean
public Binding binding(Queue queue, DirectExchange exchange) {
    return BindingBuilder.bind(queue)
                        .to(exchange)
                        .with("order.created");  // Exact match
}
```

---

**2. Topic Exchange**

- Routes based on pattern matching
- Pattern uses wildcards: `*` (one word) and `#` (zero or more words)
- Best for: Publish-subscribe with flexible routing

```
Routing keys: "order.created", "order.shipped", "order.cancelled"

Binding Pattern: "order.*"
Matches: order.created ✓, order.shipped ✓, order.cancelled ✓

Binding Pattern: "*.notification"
Matches: order.notification ✓, payment.notification ✓

Binding Pattern: "order.#"
Matches: order.created ✓, order.tracking.shipped ✓
```

```java
@Bean
public TopicExchange orderExchange() {
    return new TopicExchange("orders");
}

@Bean
public Binding binding(Queue queue, TopicExchange exchange) {
    return BindingBuilder.bind(queue)
                        .to(exchange)
                        .with("order.*");  // Pattern match
}
```

---

**3. Fanout Exchange**

- Broadcasts message to ALL bound queues
- Ignores routing key
- Best for: Broadcasting to multiple subscribers

```
Producer publishes to fanout exchange
Exchange broadcasts to:
├─ Email Queue
├─ SMS Queue
├─ Analytics Queue
└─ Log Queue
(All receive the same message)
```

```java
@Bean
public FanoutExchange broadcastExchange() {
    return new FanoutExchange("broadcasts");
}

@Bean
public Binding binding1(Queue emailQueue, FanoutExchange exchange) {
    return BindingBuilder.bind(emailQueue).to(exchange);
}

@Bean
public Binding binding2(Queue smsQueue, FanoutExchange exchange) {
    return BindingBuilder.bind(smsQueue).to(exchange);
}
// Both queues get the same message
```

---

**4. Headers Exchange**

- Routes based on message headers (not routing key)
- Match all or any headers
- Rarely used; complex for most scenarios

```java
@Bean
public HeadersExchange headersExchange() {
    return new HeadersExchange("headers-ex");
}

@Bean
public Binding binding(Queue queue, HeadersExchange exchange) {
    return BindingBuilder.bind(queue)
                        .to(exchange)
                        .whereAll(
                            Collections.singletonMap("type", "order"),
                            Collections.singletonMap("priority", "high")
                        ).match();
}
```

---

### Q7. What is a Binding and why do we need it?

**Answer:**

A **Binding** is a connection between an Exchange and a Queue. It defines the routing rule (routing key pattern) that determines which messages reach which queues.

**Without Bindings:**

```
Producer → Exchange (???) → Queue
                    Confused! Where should messages go?
```

**With Bindings:**

```
Producer → Exchange (checks bindings) → Queue A
                                     → Queue B
                                     → Queue C
(Based on routing key matching)
```

**Components of a Binding:**

- **Source**: Exchange
- **Destination**: Queue
- **Routing Key Pattern**: Rule for matching

**Example:**

```java
// Step 1: Create exchange
TopicExchange exchange = new TopicExchange("orders");

// Step 2: Create queue
Queue queue = new Queue("order-processing");

// Step 3: Create BINDING (the connection)
Binding binding = BindingBuilder.bind(queue)
                               .to(exchange)
                               .with("order.*");

// Now: messages with routing key "order.created"
// will be routed to "order-processing" queue
```

**Why Bindings Matter:**

- Decouple publishers from subscribers
- One exchange can route to multiple queues
- Multiple exchanges can route to same queue
- Dynamic routing patterns without code changes

---

### Q8. What is a Dead Letter Queue (DLQ) and when do we use it?

**Answer:**

A **Dead Letter Queue (DLQ)** is a special queue that catches messages that can't be processed normally. It's used for:

- Messages rejected by consumers
- Messages with TTL (Time-To-Live) expired
- Messages causing processing errors

**Flow:**

```
Main Queue → Consumer tries to process
                ├─ Success: Message ACKed
                └─ Failure: Message rejected/exceeds retries
                          → Dead Letter Exchange (DLX)
                              → Dead Letter Queue (DLQ)
                                  → Manual review/debugging
```

**Setup Example:**

```java
@Configuration
public class DLQConfiguration {

    // Main queue and exchange
    public static final String MAIN_QUEUE = "order-queue";
    public static final String MAIN_EXCHANGE = "order-exchange";

    // DLQ and DLX
    public static final String DLQ = "order-dlq";
    public static final String DLX = "order-dlx";

    @Bean
    public Queue mainQueue() {
        return QueueBuilder.durable(MAIN_QUEUE)
                          .withArgument("x-dead-letter-exchange", DLX)
                          .withArgument("x-dead-letter-routing-key", "dlq")
                          .build();
    }

    @Bean
    public Queue deadLetterQueue() {
        return QueueBuilder.durable(DLQ).build();
    }

    @Bean
    public TopicExchange mainExchange() {
        return new TopicExchange(MAIN_EXCHANGE);
    }

    @Bean
    public DirectExchange dlx() {
        return new DirectExchange(DLX);
    }

    @Bean
    public Binding mainBinding() {
        return BindingBuilder.bind(mainQueue())
                            .to(mainExchange())
                            .with("order.*");
    }

    @Bean
    public Binding dlqBinding() {
        return BindingBuilder.bind(deadLetterQueue())
                            .to(dlx())
                            .with("dlq");
    }
}
```

**Consumer with DLQ:**

```java
@RabbitListener(queues = "order-queue")
public void processOrder(Order order, Channel channel,
                        @Header(AmqpHeaders.DELIVERY_TAG) long tag)
                        throws IOException {
    try {
        // Process order
        validateOrder(order);
        saveOrder(order);
        channel.basicAck(tag, false);  // Success
    } catch (Exception e) {
        logger.error("Failed to process order: " + order.getId());
        // Reject and send to DLQ
        channel.basicNack(tag, false, false);
    }
}

// Consumer for DLQ (manual intervention)
@RabbitListener(queues = "order-dlq")
public void handleDeadLetter(Order order) {
    logger.error("Handling dead letter: " + order);
    // Send alert, store in database, notify admin
    alertService.notifyAdminOfFailedOrder(order);
}
```

**Real-World Scenarios:**

- Payment processing failures
- Invalid data that can't be fixed automatically
- External service unavailability
- Message corruption

---

### Q9. What is a Priority Queue and how does it work?

**Answer:**

A **Priority Queue** ensures high-priority messages are processed before low-priority ones. Useful when:

- Critical orders must be processed before regular orders
- VIP customer requests need priority
- System overload requires triaging

**Setup:**

```java
@Bean
public Queue priorityQueue() {
    return QueueBuilder.durable("priority-queue")
                      .maxPriority(10)  // Priority scale: 0-10
                      .build();
}
```

**Publishing with Priority:**

```java
@Bean
public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {
    RabbitTemplate template = new RabbitTemplate(connectionFactory);
    // Set default priority
    template.setDefaultReceiveTimeout(5000);
    return template;
}

// Publish with priority
public void sendOrder(Order order, int priority) {
    rabbitTemplate.convertAndSend("priority-queue", order, message -> {
        message.getMessageProperties().setPriority(priority);
        return message;
    });
}

// Usage:
sendOrder(regularOrder, 3);    // Regular priority
sendOrder(vipOrder, 10);       // High priority
```

**Important Notes:**

- Consumer must be idle for priority to take effect
- If consumer is actively consuming, order is FIFO
- Higher number = higher priority
- Use only when necessary (slight performance overhead)

---

### Q10. Explain Message TTL (Time-To-Live) and its use cases.

**Answer:**

**TTL (Time-To-Live)** specifies how long a message can exist in a queue before being discarded or sent to DLQ.

**Types:**

1. **Queue TTL**: Applied to entire queue
2. **Message TTL**: Applied to individual messages

**Queue-level TTL:**

```java
@Bean
public Queue queueWithTTL() {
    return QueueBuilder.durable("temporary-queue")
                      .ttl(60000)  // 60 seconds
                      .build();
}
// All messages expire after 60 seconds
```

**Message-level TTL:**

```java
public void sendMessageWithTTL(String message) {
    rabbitTemplate.convertAndSend("my-exchange", "my-key", message, msg -> {
        msg.getMessageProperties().setExpiration("30000");  // 30 seconds
        return msg;
    });
}
```

**Use Cases:**

| Scenario               | TTL              | Reason                            |
| ---------------------- | ---------------- | --------------------------------- |
| OTP verification code  | 5 min            | Code validity window              |
| One-time notifications | 1 hour           | Stale notifications should expire |
| Cache invalidation     | 2 hours          | Cached data becomes stale         |
| Session-based messages | Session duration | Auto-cleanup                      |

**Expired Message Handling:**

```java
@Bean
public Queue queueWithDLQ() {
    return QueueBuilder.durable("orders")
                      .ttl(60000)
                      .deadLetterExchange("dlx")
                      .build();
}
// When message TTL expires, it goes to DLQ instead of being deleted
```

---

### Q11. What is Queue Expiration and how is it different from Message TTL?

**Answer:**

**Queue Expiration (x-expires):**

- Queue itself is deleted if **no activity** for specified time
- After expiration, queue is removed entirely
- All remaining messages are lost (unless DLQ configured)

**Message TTL:**

- Individual messages expire
- Queue still exists
- Expired messages are removed (or sent to DLQ)

**Comparison:**

```java
// Queue Expiration (queue deleted after 30 min of inactivity)
@Bean
public Queue autoExpiringQueue() {
    return QueueBuilder.durable("session-queue")
                      .withArgument("x-expires", 1800000)  // 30 min in ms
                      .build();
}

// Message TTL (each message lives 5 min)
@Bean
public Queue messageTTLQueue() {
    return QueueBuilder.durable("notification-queue")
                      .ttl(300000)  // 5 min in ms
                      .build();
}
```

**Scenario Example:**

```
Scenario: Temporary queue for user session

Using Queue Expiration:
User logs in → Queue created
User active → No expiration (has activity)
User abandons session for 30 min → Queue auto-deleted
User logs in again → New queue created

Using Message TTL:
User logs in → Queue created
Messages sent with 5-min TTL
User inactive, but queue remains (even if empty)
Wasted resources
```

**Difference Table:**

| Feature             | Message TTL            | Queue Expiration     |
| ------------------- | ---------------------- | -------------------- |
| **What Expires**    | Individual messages    | Entire queue         |
| **Trigger**         | Time passes            | No activity period   |
| **Cleanup**         | Message removed/to DLQ | Queue deleted        |
| **Resource Impact** | Low (just messages)    | High (queue cleanup) |
| **Use Case**        | Time-limited content   | Temporary resources  |

---

### Q12. How does message ordering work in RabbitMQ?

**Answer:**

**Message Ordering Guarantee:**
RabbitMQ guarantees messages are delivered to consumers **in the order they were received** ONLY if:

1. **Single Consumer**: One consumer per queue (no parallelization)
2. **Sequential Processing**: Messages processed one at a time
3. **No Reordering**: No parallel/async processing

**Ordered Scenario:**

```
Queue: [M1] → [M2] → [M3] → [M4]
Consumer (single): M1 → M2 → M3 → M4
Order: ✓ Guaranteed
```

**Non-Ordered Scenario (Multiple Consumers):**

```
Queue: [M1] → [M2] → [M3] → [M4]
Consumer A: M1, M3
Consumer B: M2, M4
Order: ✗ NOT guaranteed (interleaved)
```

**Java Example - Ordered Processing:**

```java
// Single consumer = Ordered
@RabbitListener(queues = "order-queue", concurrency = "1")
public void processOrderSequential(Order order) {
    System.out.println("Processing: " + order.getId());
    // Processing happens sequentially
    // Order preserved
}

// Multiple consumers = NOT ordered
@RabbitListener(queues = "order-queue", concurrency = "5")
public void processOrderParallel(Order order) {
    System.out.println("Processing: " + order.getId());
    // 5 threads process simultaneously
    // Order NOT preserved
}
```

**Strategies to Maintain Order:**

1. **Single Consumer**: Simple but slow
2. **Hash-Based Routing**: Same order → same consumer
   ```java
   int consumerId = order.customerId % numConsumers;
   // All orders from same customer go to same consumer
   ```
3. **Sharding by Partition Key**:
   ```java
   // Use routing key to shard orders
   String routingKey = "order." + order.customerId;
   // Same customer's orders go to same queue/consumer
   ```

---

## **SECTION 3: MESSAGE DELIVERY & ACKNOWLEDGMENT (Q13-Q18)**

### Q13. Explain message acknowledgment modes (Auto, Manual, None).

**Answer:**

**Acknowledgment** tells RabbitMQ that consumer successfully processed a message. Controls what happens if consumer crashes before finishing.

**1. Auto Acknowledgment (Automatic)**

- RabbitMQ assumes message is processed immediately after delivery
- If consumer crashes before actual processing, message is lost
- **Risk**: Message loss on failure

```java
@RabbitListener(queues = "orders", ackMode = AcknowledgeMode.AUTO)
public void processOrder(Order order) {
    // RabbitMQ auto-acks immediately
    // If this crashes → message lost!
    deleteFromDatabase(order.getId());  // If crashes here → data inconsistent
}
```

**2. Manual Acknowledgment (Explicit)**

- Consumer explicitly sends ACK after processing
- If consumer crashes before ACK, RabbitMQ redelivers message
- **Safe**: No message loss
- **Best Practice**: Use this for critical operations

```java
@RabbitListener(queues = "orders", ackMode = AcknowledgeMode.MANUAL)
public void processOrder(Order order, Channel channel,
                        @Header(AmqpHeaders.DELIVERY_TAG) long deliveryTag)
                        throws IOException {
    try {
        // Process order
        saveOrder(order);
        validateOrder(order);

        // Explicit ACK only after success
        channel.basicAck(deliveryTag, false);
    } catch (Exception e) {
        logger.error("Failed to process: " + order.getId());
        // NACK: Reject and requeue
        channel.basicNack(deliveryTag, false, true);  // true = requeue
    }
}
```

**3. NONE (No Acknowledgment)**

- No acknowledgment mechanism
- Message considered delivered once sent
- **Rare**: Only for fire-and-forget scenarios

```java
@RabbitListener(queues = "logs", ackMode = AcknowledgeMode.NONE)
public void processLog(String logMessage) {
    // RabbitMQ doesn't care if this processes or not
    System.out.println(logMessage);
}
```

**Comparison Table:**

| Mode            | Auto Acknowledge       | Manual Ack       | None       |
| --------------- | ---------------------- | ---------------- | ---------- |
| **Safety**      | ❌ Low (messages lost) | ✅ High (safe)   | ❌ None    |
| **Redelivery**  | ❌ No                  | ✅ Yes           | ❌ No      |
| **Performance** | ✅ Fast                | ⚠️ Slower        | ✅ Fastest |
| **Use Case**    | Non-critical logs      | Orders, payments | Analytics  |

---

### Q14. What is the difference between basicAck, basicNack, and basicReject?

**Answer:**

All three send feedback to RabbitMQ about message processing outcome, but with different effects.

**basicAck (Positive Acknowledgment)**

- ✅ Message processed successfully
- Removes message from queue
- Cannot be redelivered
- Used for successful processing

```java
try {
    processOrder(order);
    channel.basicAck(deliveryTag, false);  // Success!
} catch (Exception e) {
    // Not ACK'ed
}
```

**basicNack (Negative Acknowledgment)**

- ❌ Message processing failed
- Can requeue message (try again) or discard (send to DLQ)
- More flexible than basicReject
- Allows batch NACK

```java
try {
    processOrder(order);
    channel.basicAck(deliveryTag, false);
} catch (Exception e) {
    // Option 1: Requeue (try again later)
    channel.basicNack(deliveryTag, false, true);  // true = requeue

    // Option 2: Send to DLQ (don't retry)
    channel.basicNack(deliveryTag, false, false);  // false = don't requeue
}
```

**basicReject (Explicit Rejection)**

- ❌ Explicitly reject single message
- Must requeue or discard (limited options)
- Older, less flexible than basicNack
- Cannot batch reject

```java
if (order.isEmpty()) {
    channel.basicReject(deliveryTag, false);  // Reject, don't requeue
    // Message goes to DLX/DLQ
}
```

**Comparison:**

```java
// Scenario: Payment processing

Channel.basicAck()
├─ Payment successful
└─ Remove from queue

Channel.basicNack(deliveryTag, false, true)
├─ Temporary error (service down)
└─ Requeue → Try again later

Channel.basicNack(deliveryTag, false, false)
├─ Validation error (invalid card)
└─ Send to DLQ → Manual review

Channel.basicReject(deliveryTag, false)
├─ Old style (same as basicNack with false)
└─ Avoid; use basicNack instead
```

**Multiple Message Handling:**

```java
// NACK multiple messages at once
channel.basicNack(deliveryTag, true, false);
// Rejects this message AND all previous unacked messages
// Second param: true = multiple
```

---

### Q15. What is Prefetch and how does it affect performance?

**Answer:**

**Prefetch (QoS - Quality of Service)** specifies how many messages RabbitMQ sends to consumer before waiting for acknowledgment.

**How It Works:**

```
Prefetch = 1:
Queue: [M1] → Consumer A (M1 received, waiting)
Queue: [M2] → Consumer B (M2 received, waiting)
Queue: [M3] → Waiting for ack from Consumer A
(One message per consumer at a time)

Prefetch = 10:
Queue: [M1-M10] → Consumer A (received all 10, processing in memory)
Queue: [M11-M20] → Consumer B (received all 10)
(Up to 10 messages per consumer in memory)
```

**Configuration:**

```java
@Configuration
public class RabbitConfig {

    @Bean
    public SimpleMessageListenerContainer container(
            ConnectionFactory connectionFactory) {
        SimpleMessageListenerContainer container =
                new SimpleMessageListenerContainer(connectionFactory);

        container.setPrefetchCount(10);  // Set prefetch
        container.setDefaultRequeueRejected(true);
        return container;
    }
}

// Or in listener
@RabbitListener(queues = "orders", concurrency = "1-5")
public void processOrder(Order order) {
    // Uses prefetch count from container
}
```

**Impact on Different Scenarios:**

| Prefetch | Scenario                                | Effect                                       |
| -------- | --------------------------------------- | -------------------------------------------- |
| **1**    | Heavy processing (each msg takes 10s)   | ✅ Fair distribution, ❌ Slow                |
| **1**    | Light processing (each msg takes 100ms) | ❌ Inefficient network                       |
| **10**   | Balanced workload                       | ✅ Good performance                          |
| **100**  | Light processing                        | ✅ High throughput, ❌ Memory spike on crash |

**Real-World Example:**

```java
// Slow consumer (payment verification takes 5s)
@Configuration
public class SlowConsumerConfig {
    @Bean
    public SimpleMessageListenerContainer slowContainer(
            ConnectionFactory connectionFactory) {
        SimpleMessageListenerContainer container =
                new SimpleMessageListenerContainer(connectionFactory);
        container.setQueues(paymentQueue);
        container.setPrefetchCount(1);  // Process one at a time
        container.setConcurrentConsumers(5);  // But use 5 consumers
        return container;
    }
}

// Fast consumer (log processing takes 10ms)
@Configuration
public class FastConsumerConfig {
    @Bean
    public SimpleMessageListenerContainer fastContainer(
            ConnectionFactory connectionFactory) {
        SimpleMessageListenerContainer container =
                new SimpleMessageListenerContainer(connectionFactory);
        container.setQueues(logQueue);
        container.setPrefetchCount(100);  // Batch more messages
        container.setConcurrentConsumers(10);
        return container;
    }
}
```

**Memory Considerations:**

```
If prefetch = 100 and message size = 1MB
Consumer loads: 100 * 1MB = 100MB into memory
If consumer crashes before ACK → 100MB of data lost
```

---

### Q16. What is requeue and infinite loop problem?

**Answer:**

**Requeue**: When NACK with `requeue=true`, message goes back to queue for retry.

**Problem:**
If consumer always fails, message gets requeued infinitely, blocking queue.

```
Consumer crashes on Order#123
NACK with requeue=true
Message goes back to queue
Consumer retries Order#123
Crashes again...
Infinite loop! Queue stuck!
```

**Infinite Loop Scenario:**

```java
@RabbitListener(queues = "orders")
public void processOrder(Order order, Channel channel,
                        @Header(AmqpHeaders.DELIVERY_TAG) long tag)
                        throws IOException {
    try {
        // This always fails!
        validateOrder(order);  // Throws exception for invalid orders
        channel.basicAck(tag, false);
    } catch (Exception e) {
        // Requeue indefinitely
        channel.basicNack(tag, false, true);  // ❌ Infinite loop!
    }
}

// Result: Order#123 requeued → crash → requeue → crash...
```

**Solutions:**

**1. Use DLQ with Retry Limit:**

```java
@Bean
public Queue orderQueue() {
    return QueueBuilder.durable("orders")
                      .deadLetterExchange("dlx")
                      .build();
}

@RabbitListener(queues = "orders")
public void processOrder(Order order, Channel channel,
                        @Header(AmqpHeaders.DELIVERY_TAG) long tag)
                        throws IOException {
    try {
        validateOrder(order);
        channel.basicAck(tag, false);
    } catch (InvalidOrderException e) {
        // Don't requeue invalid orders
        // Send to DLQ instead
        channel.basicNack(tag, false, false);  // false = don't requeue
    } catch (TemporaryException e) {
        // Requeue for temporary errors
        channel.basicNack(tag, false, true);
    }
}
```

**2. Track Retry Attempts:**

```java
@RabbitListener(queues = "orders")
public void processOrder(Order order, Channel channel,
                        @Header(AmqpHeaders.DELIVERY_TAG) long tag,
                        @Headers Map<String, ?> headers)
                        throws IOException {
    try {
        processOrder(order);
        channel.basicAck(tag, false);
    } catch (RetryableException e) {
        int retryCount = getRetryCount(headers);

        if (retryCount < 3) {  // Max 3 retries
            // Increment and requeue
            channel.basicNack(tag, false, true);
        } else {
            // Max retries exceeded, send to DLQ
            channel.basicNack(tag, false, false);
        }
    }
}

private int getRetryCount(Map<String, ?> headers) {
    Long xDeath = (Long) headers.get("x-death");
    // x-death tracks requeue attempts
    return xDeath != null ? xDeath.intValue() : 0;
}
```

**3. Use Spring Retry with Backoff:**

```java
@Configuration
public class RetryConfiguration {

    @Bean
    public RabbitTemplate rabbitTemplate(ConnectionFactory factory) {
        RabbitTemplate template = new RabbitTemplate(factory);

        // Retry policy: exponential backoff
        RetryTemplate retryTemplate = new RetryTemplate();
        ExponentialBackOffPolicy backOffPolicy = new ExponentialBackOffPolicy();
        backOffPolicy.setInitialInterval(1000);    // 1 second
        backOffPolicy.setMultiplier(2.0);          // Double each retry
        backOffPolicy.setMaxInterval(10000);       // Max 10 seconds
        retryTemplate.setBackOffPolicy(backOffPolicy);

        SimpleRetryPolicy retryPolicy = new SimpleRetryPolicy();
        retryPolicy.setMaxAttempts(3);
        retryTemplate.setRetryPolicy(retryPolicy);

        template.setRetryTemplate(retryTemplate);
        return template;
    }
}

@RabbitListener(queues = "orders")
public void processOrder(Order order) {
    // Spring handles retries automatically
    // No manual NACK needed
    validateOrder(order);
}
```

---

### Q17. What is the purpose of x-death header and how is it used?

**Answer:**

**x-death** is a RabbitMQ system header that tracks requeue attempts. It records:

- How many times message was requeued
- Queue it came from
- Timestamp of each requeue

**Format:**

```
x-death: [
    {
        "count": 3,              // Requeued 3 times
        "reason": "expired",     // Why rejected
        "queue": "orders",       // Original queue
        "time": timestamp,       // When rejected
        "exchange": "order-ex",  // Original exchange
        "routing-keys": ["order.created"]
    }
]
```

**Usage Example:**

```java
@RabbitListener(queues = "orders")
public void processOrder(Order order, Channel channel,
                        @Header(AmqpHeaders.DELIVERY_TAG) long tag,
                        @Headers Map<String, ?> headers)
                        throws IOException {
    try {
        validateAndProcess(order);
        channel.basicAck(tag, false);
    } catch (TemporaryException e) {
        // Get death count
        Integer deathCount = extractDeathCount(headers);

        if (deathCount < 5) {
            // Continue retrying
            channel.basicNack(tag, false, true);
        } else {
            // Too many retries, give up
            logger.error("Max retries exceeded for order: " + order.getId());
            channel.basicNack(tag, false, false);  // Send to DLQ
        }
    }
}

private Integer extractDeathCount(Map<String, ?> headers) {
    List<Map<String, ?>> deathList =
            (List<Map<String, ?>>) headers.get("x-death");

    if (deathList != null && !deathList.isEmpty()) {
        Map<String, ?> death = deathList.get(0);
        Long count = (Long) death.get("count");
        return count != null ? count.intValue() : 0;
    }
    return 0;
}
```

**Real-World Scenario:**

```java
@Bean
public Queue orderQueueWithDLX() {
    return QueueBuilder.durable("orders")
                      .ttl(30000)  // 30 sec message TTL
                      .deadLetterExchange("dlx")
                      .build();
}

// x-death helps track:
// Attempt 1: Sent to queue, fails, requeued
// Attempt 2: Processing fails, requeued
// Attempt 3: External service down, requeued
// Attempt 4: Consumer offline, TTL expires, sent to DLQ
// DLQ consumer can see all retry history in x-death
```

---

### Q18. Explain lazy queue and its performance implications.

**Answer:**

**Lazy Queue (x-queue-mode: lazy)** stores messages on disk instead of memory by default.

**Normal Queue (Default):**

- Messages in memory first
- Move to disk only when needed (memory full)
- Faster access
- Higher memory usage

**Lazy Queue:**

- Messages on disk from start
- Move to memory only when consumed
- Slower access
- Lower memory usage
- Best for large queues with slow consumption

**Configuration:**

```java
@Bean
public Queue normalQueue() {
    return QueueBuilder.durable("normal-queue")
                      .build();  // Default: x-queue-mode = default
}

@Bean
public Queue lazyQueue() {
    return QueueBuilder.durable("lazy-queue")
                      .lazy()  // x-queue-mode = lazy
                      .build();
}
```

**Performance Comparison:**

| Aspect           | Normal                  | Lazy                          |
| ---------------- | ----------------------- | ----------------------------- |
| **Throughput**   | ✅ High                 | ❌ Lower                      |
| **Memory**       | ❌ High                 | ✅ Low                        |
| **Access Speed** | ✅ Fast                 | ⚠️ Slower (disk I/O)          |
| **Suitable For** | Real-time, low volume   | Bulk data, offline processing |
| **Example**      | Real-time notifications | Log collection, batch jobs    |

**Benchmark Example:**

```
Scenario: Queue 100,000 messages (1MB each = 100GB)

Normal Queue:
├─ Memory usage: Spikes to available RAM (e.g., 16GB) ⚠️
├─ Throughput: 10,000 msg/sec ✅
├─ Latency: <1ms ✅
└─ Risk: Out of memory crash

Lazy Queue:
├─ Memory usage: Stable, ~1GB ✅
├─ Throughput: 5,000 msg/sec ⚠️
├─ Latency: 5-10ms (disk I/O)
└─ Reliability: Survives memory pressure ✅
```

**When to Use:**

```java
// Lazy Queue: Bulk log ingestion
@Bean
public Queue bulkLogQueue() {
    return QueueBuilder.durable("logs")
                      .lazy()  // Millions of small logs
                      .build();
}

// Normal Queue: Real-time payments
@Bean
public Queue paymentQueue() {
    return QueueBuilder.durable("payments")
                      // Keep default for speed
                      .build();
}
```

---

## **SECTION 4: HIGH AVAILABILITY & CLUSTERING (Q19-Q23)**

### Q19. What are RabbitMQ Clusters and how do they work?

**Answer:**

A **RabbitMQ Cluster** is multiple RabbitMQ nodes working together to:

- Distribute load across nodes
- Provide failover (if one node fails, others continue)
- Increase throughput and availability

**Architecture:**

```
┌──────────────────────────────────────────┐
│     RabbitMQ Cluster (3 nodes)           │
├──────────────────────────────────────────┤
│ Node 1 (node1@host1)    Node 2 (node2@host2)
│  ├─ Mgmt plugin          ├─ Worker
│  ├─ Queue: orders        └─ Replicates state
│  └─ Exchange: payments
│                                Node 3 (node3@host3)
│                                 ├─ Worker
│                                 └─ Replicates state
│
└──────────────────────────────────────────┘
```

**Key Concepts:**

**1. Cluster Formation:**

```bash
# Start node1
rabbitmq-server -nodename node1@host1

# Join node2 to cluster
rabbitmq-server -nodename node2@host2
rabbitmqctl cluster_status  # See cluster info

# Make node2 join node1's cluster
rabbitmqctl -n node2 join_cluster node1@host1
```

**2. Shared Resources:**

- **Exchanges**: Shared across all nodes ✅
- **Queue Metadata**: Shared (queue declaration visible everywhere)
- **Queue Data**: By default, **not replicated** ❌ (only on declaring node)

**3. Queue Replication (Critical!):**

```java
// Without replication: Queue lives only on node1
Queue queue = new Queue("orders");  // Created on node1
// If node1 crashes → Queue lost, messages lost!

// With replication: Queue on node1 + replicas on node2, node3
@Bean
public Queue replicatedQueue() {
    return QueueBuilder.durable("orders")
                      .withArgument("x-queue-type", "quorum")
                      // OR
                      // .withArgument("x-ha-policy", "all")
                      .build();
}
```

**Classic HA vs Quorum Queues:**

```java
// Classic HA (older, synchronous replication)
@Bean
public Queue classicHAQueue() {
    return QueueBuilder.durable("orders")
                      .withArgument("x-ha-policy", "all")  // Replicate to all
                      .build();
}

// Quorum Queues (newer, async replication, fault-tolerant)
@Bean
public Queue quorumQueue() {
    return QueueBuilder.durable("orders")
                      .withArgument("x-queue-type", "quorum")
                      .withArgument("x-quorum-initial-group-size", 3)
                      .build();
}
```

**Cluster Benefits:**

- ✅ Failover: If node1 down, switch to node2/node3
- ✅ Horizontal scaling: Add nodes for more capacity
- ✅ Load distribution: Consumers across multiple nodes
- ❌ Complexity: More to manage, coordinate, debug

---

### Q20. What is the difference between Mirrored Queues and Quorum Queues?

**Answer:**

Both provide queue replication (HA), but with different trade-offs.

**Mirrored Queues (x-ha-policy: all):**

- Master queue + mirror replicas
- Synchronous replication
- All nodes have same data at all times
- If master fails, quick promotion of mirror
- Higher latency due to sync waiting

**Configuration:**

```java
@Bean
public Queue mirroredQueue() {
    return QueueBuilder.durable("orders")
                      .withArgument("x-ha-policy", "all")  // All nodes
                      .withArgument("x-ha-sync-mode", "automatic")
                      .build();
}
```

**Quorum Queues (x-queue-type: quorum):**

- No master/replica distinction
- Asynchronous replication
- Requires quorum (majority of nodes) to confirm write
- Automatic leader election
- More resilient to failure
- Recommended for modern RabbitMQ

**Configuration:**

```java
@Bean
public Queue quorumQueue() {
    return QueueBuilder.durable("orders")
                      .withArgument("x-queue-type", "quorum")
                      .withArgument("x-quorum-initial-group-size", 3)
                      .build();
}
```

**Comparison:**

| Feature                 | Mirrored                  | Quorum                    |
| ----------------------- | ------------------------- | ------------------------- |
| **Sync Mode**           | Synchronous               | Asynchronous              |
| **Latency**             | ⚠️ Higher (waits for all) | ✅ Lower                  |
| **Consistency**         | ✅ Strong                 | ⚠️ Eventual               |
| **Node Failure**        | Master fails = promotion  | Automatic leader election |
| **Partition Tolerance** | ❌ Can lose quorum        | ✅ Better handling        |
| **Recommended**         | ❌ Deprecated             | ✅ Modern (3.8+)          |

**Failure Scenario:**

```
Mirrored Queue (3 nodes: master + 2 mirrors)
├─ Master fails
├─ One mirror promotes to master
└─ New replica spawned on 3rd node
├─ Total writes blocked during promotion ⚠️

Quorum Queue (3 nodes: leader + 2 followers)
├─ Leader fails
├─ Followers detect failure
├─ New leader elected automatically ✅
└─ Writes continue via quorum (2/3 nodes)
└─ No interruption ✅
```

**Recommendation:**

```java
// Use Quorum for new applications
@Bean
public Queue modernQueue() {
    return QueueBuilder.durable("orders")
                      .withArgument("x-queue-type", "quorum")
                      .build();
}

// Use Mirrored only for legacy systems (RabbitMQ < 3.8)
```

---

### Q21. What is RabbitMQ Federation and how is it different from clustering?

**Answer:**

**Federation** connects separate RabbitMQ clusters (or brokers) to share messages across different locations/data centers.

**Clustering vs Federation:**

| Aspect           | Clustering            | Federation                     |
| ---------------- | --------------------- | ------------------------------ |
| **Scope**        | Same data center      | Different data centers         |
| **Network**      | LAN (low latency)     | WAN (high latency)             |
| **Topology**     | All nodes together    | Independent clusters connected |
| **Fault Domain** | Shared                | Separate                       |
| **Use Case**     | HA within data center | Multi-DC, disaster recovery    |

**Visual:**

```
Clustering (Single data center):
┌─ Cluster 1 ─────────────────┐
│ Node1 ↔ Node2 ↔ Node3      │ (Tight coupling)
└─────────────────────────────┘

Federation (Multiple data centers):
┌─ Cluster DC1 ─────┐      ┌─ Cluster DC2 ─────┐
│ Node1 ↔ Node2    │─────│ Node3 ↔ Node4     │
└──────────────────┘      └───────────────────┘
        ↑                          ↑
     (Independent)            (Independent)
     Queue: orders        Subscribes to DC1's orders
```

**Federation Setup:**

```java
// Declare upstream (source broker)
// In RabbitMQ management UI or config:
// rabbitmq.conf:
// federation.upstream.primary.uri = amqp://primary.datacenter.com
// federation.upstream.primary.prefetch-count = 1000

// Declare downstream (target broker)
@Bean
public TopicExchange federatedExchange() {
    return ExchangeBuilder.topicExchange("orders")
                         .durable(true)
                         .build();
}

// Enable federation on exchange
// Via policy or management API
```

**Use Cases:**

```
Scenario 1: Multi-region deployment
┌─ US Data Center      ┌─ EU Data Center
│ Orders created       │ Orders forwarded
│ Payments processed   │ (Eventually consistent)
└────────────────────→└─

Scenario 2: Disaster recovery
┌─ Primary DC         ┌─ Standby DC
│ Active cluster      │ Federation replicates
│ (normal operation)  │ (ready to failover)
└────────────────────→└─

Scenario 3: Decoupled teams
┌─ Team A Cluster     ┌─ Team B Cluster
│ Order service       │ Analytics service
│                     │ (subscribes to orders)
└────────────────────→└─
```

---

### Q22. What is a RabbitMQ Shovel and how is it different from Federation?

**Answer:**

**Shovel** is similar to Federation but with more control over message routing between brokers.

**Shovel vs Federation:**

| Feature           | Shovel               | Federation                |
| ----------------- | -------------------- | ------------------------- |
| **Granularity**   | Queue-to-queue       | Exchange-to-exchange      |
| **Configuration** | Per-queue binding    | Per-exchange policy       |
| **Control**       | Fine-grained routing | Automatic with rules      |
| **Use Case**      | One-way migration    | Bidirectional replication |
| **Complexity**    | More code            | More declarative          |

**Shovel Configuration:**

```java
// rabbitmq.conf
# Define shovel: source queue to destination
shovel.my_shovel.sources.1 = amqp://source-broker.com
shovel.my_shovel.destinations.1 = amqp://dest-broker.com

shovel.my_shovel.source_queue = orders
shovel.my_shovel.destination_exchange = orders_target
shovel.my_shovel.ack_mode = on_confirm
```

**Real-World Scenarios:**

```
Shovel: Unidirectional data movement
┌─ Legacy Broker ──Shovel──→ ┌─ New Broker
│ Old orders queue          │ New orders queue
└───────────────────────────→└─
(Gradual migration)

Federation: Multi-directional sharing
┌─ DC1 Broker ←→ Federation ←→ ┌─ DC2 Broker
│ orders exchange             │ orders exchange
└───────────────────────────→└─
(Continuous sync)
```

**When to Use:**

- **Shovel**: Migrating data between RabbitMQ versions, broker upgrades
- **Federation**: Multi-region deployment, cross-team message sharing

---

### Q23. How do you handle message persistence in RabbitMQ?

**Answer:**

**Message Persistence** ensures messages survive broker restarts. Two levels:

**1. Queue Durability:**

```java
@Bean
public Queue durableQueue() {
    return QueueBuilder.durable("orders")  // Survives restart
                      .build();
}

@Bean
public Queue ephemeralQueue() {
    return QueueBuilder.nonDurable("temp-queue")  // Lost on restart
                      .build();
}
```

**2. Message Delivery Mode:**

```java
// Persistent message (survives restart)
rabbitTemplate.convertAndSend("orders", "key", message, msg -> {
    msg.getMessageProperties().setDeliveryMode(
        MessageDeliveryMode.PERSISTENT  // Durable
    );
    return msg;
});

// Non-persistent message (lost on restart)
rabbitTemplate.convertAndSend("orders", "key", message, msg -> {
    msg.getMessageProperties().setDeliveryMode(
        MessageDeliveryMode.NON_PERSISTENT  // Transient
    );
    return msg;
});
```

**Complete Persistence Setup:**

```java
@Configuration
public class PersistenceConfig {

    @Bean
    public Queue persistentQueue() {
        return QueueBuilder.durable("orders")
                          .build();
    }

    @Bean
    public Exchange persistentExchange() {
        return ExchangeBuilder.topicExchange("orders")
                             .durable(true)
                             .build();
    }

    @Bean
    public Binding persistentBinding() {
        return BindingBuilder.bind(persistentQueue())
                            .to(persistentExchange())
                            .with("order.*");
    }
}

@Service
public class OrderService {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void publishOrder(Order order) {
        rabbitTemplate.convertAndSend("orders", "order.created", order, msg -> {
            // Make message persistent
            msg.getMessageProperties().setDeliveryMode(
                MessageDeliveryMode.PERSISTENT
            );
            // Set expiration
            msg.getMessageProperties().setExpiration("3600000");  // 1 hour
            return msg;
        });
    }
}

@RabbitListener(queues = "orders", ackMode = AcknowledgeMode.MANUAL)
public void processOrder(Order order, Channel channel,
                        @Header(AmqpHeaders.DELIVERY_TAG) long tag)
                        throws IOException {
    try {
        // Process
        saveOrder(order);
        channel.basicAck(tag, false);
    } catch (Exception e) {
        // Requeue for retry
        channel.basicNack(tag, false, true);
    }
}
```

**Persistence Guarantees:**

| Setup                                | Durability   | Risk                      |
| ------------------------------------ | ------------ | ------------------------- |
| Queue durable + message persistent   | ✅ Very High | Minimal (disk I/O)        |
| Queue durable + message transient    | ⚠️ Medium    | Messages lost             |
| Queue ephemeral + message persistent | ⚠️ Medium    | Queue lost, then messages |
| Queue ephemeral + message transient  | ❌ Low       | Everything lost           |

---

## **SECTION 5: SPRING BOOT INTEGRATION (Q24-Q27)**

### Q24. How do you configure RabbitMQ in Spring Boot?

**Answer:**

**Step 1: Add Dependency**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

**Step 2: Application Properties**

```properties
# application.properties
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
spring.rabbitmq.virtual-host=/

# Connection pool
spring.rabbitmq.connection-factory.cache-mode=CHANNEL
spring.rabbitmq.connection-factory.channel-cache-size=50

# Listener config
spring.rabbitmq.listener.simple.concurrency=5
spring.rabbitmq.listener.simple.max-concurrency=10
spring.rabbitmq.listener.simple.prefetch=1
spring.rabbitmq.listener.simple.default-requeue-rejected=false
spring.rabbitmq.listener.simple.acknowledge-mode=MANUAL
```

**Step 3: Configuration Class**

```java
@Configuration
public class RabbitMQConfig {

    // Exchanges
    @Bean
    public TopicExchange orderExchange() {
        return new TopicExchange("orders", true, false);
    }

    // Queues
    @Bean
    public Queue orderQueue() {
        return new Queue("order-queue", true);
    }

    // Bindings
    @Bean
    public Binding orderBinding(Queue orderQueue, TopicExchange orderExchange) {
        return BindingBuilder.bind(orderQueue)
                            .to(orderExchange)
                            .with("order.*");
    }

    // RabbitTemplate for publishing
    @Bean
    public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {
        RabbitTemplate template = new RabbitTemplate(connectionFactory);
        template.setExchange("orders");
        template.setDefaultReceiveTimeout(5000);
        return template;
    }
}
```

**Step 4: Producer**

```java
@Service
public class OrderProducer {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void sendOrder(Order order) {
        rabbitTemplate.convertAndSend("orders", "order.created", order, msg -> {
            msg.getMessageProperties().setDeliveryMode(MessageDeliveryMode.PERSISTENT);
            return msg;
        });
    }
}
```

**Step 5: Consumer**

```java
@Service
public class OrderConsumer {

    @RabbitListener(queues = "order-queue")
    public void handleOrder(Order order) {
        System.out.println("Processing order: " + order.getId());
        // Process order
    }
}
```

---

### Q25. What is @RabbitListener and how does it work?

**Answer:**

**@RabbitListener** is a Spring annotation that marks a method as RabbitMQ message consumer.

**Basic Usage:**

```java
@RabbitListener(queues = "order-queue")
public void handleOrder(Order order) {
    System.out.println("Order received: " + order.getId());
}
```

**With Multiple Queues:**

```java
@RabbitListener(queues = {"order-queue", "payment-queue"})
public void handleMessage(Message message) {
    // Processes messages from both queues
}
```

**With Concurrency:**

```java
@RabbitListener(queues = "order-queue",
               concurrency = "5")
public void handleOrder(Order order) {
    // Processes up to 5 messages concurrently
}

// Or: concurrency = "2-10" (min 2, max 10)
@RabbitListener(queues = "order-queue",
               concurrency = "2-10")
public void handleOrder(Order order) {
}
```

**With Manual Acknowledgment:**

```java
@RabbitListener(queues = "order-queue",
               ackMode = AcknowledgeMode.MANUAL)
public void handleOrder(Order order, Channel channel,
                       @Header(AmqpHeaders.DELIVERY_TAG) long deliveryTag)
                       throws IOException {
    try {
        processOrder(order);
        channel.basicAck(deliveryTag, false);
    } catch (Exception e) {
        channel.basicNack(deliveryTag, false, true);
    }
}
```

**Extract Headers:**

```java
@RabbitListener(queues = "order-queue")
public void handleOrder(
    Order order,
    @Header("x-custom-header") String customHeader,
    @Headers Map<String, ?> headers) {

    System.out.println("Custom header: " + customHeader);
    System.out.println("All headers: " + headers);
}
```

**Advanced Configuration:**

```java
@RabbitListener(
    queues = "order-queue",
    concurrency = "5",
    ackMode = AcknowledgeMode.MANUAL,
    priority = "10",  // Higher priority listener
    exclusive = false
)
public void handleOrder(Order order, Channel channel,
                       @Header(AmqpHeaders.DELIVERY_TAG) long tag)
                       throws IOException {
    try {
        validateAndProcess(order);
        channel.basicAck(tag, false);
    } catch (ValidationException e) {
        logger.error("Validation failed: " + e.getMessage());
        channel.basicNack(tag, false, false);  // Send to DLQ
    } catch (TemporaryException e) {
        logger.warn("Temporary error, retrying");
        channel.basicNack(tag, false, true);   // Requeue
    }
}
```

**Class-Level @RabbitListener:**

```java
@Component
@RabbitListener(queues = "order-queue")
public class OrderConsumer {

    @RabbitHandler
    public void handleOrder(Order order) {
        // Handles Order type
    }

    @RabbitHandler
    public void handleString(String message) {
        // Handles String type (content-type based dispatch)
    }
}
```

---

### Q26. How do you handle errors and exceptions in RabbitMQ listeners?

**Answer:**

**Strategy 1: Manual Acknowledgment (Most Control)**

```java
@RabbitListener(queues = "orders", ackMode = AcknowledgeMode.MANUAL)
public void handleOrder(Order order, Channel channel,
                       @Header(AmqpHeaders.DELIVERY_TAG) long tag)
                       throws IOException {
    try {
        validateOrder(order);
        saveOrder(order);
        channel.basicAck(tag, false);  // Success

    } catch (ValidationException e) {
        logger.error("Invalid order");
        channel.basicNack(tag, false, false);  // Reject, send to DLQ

    } catch (TemporaryException e) {
        logger.warn("Temporary error, will retry");
        channel.basicNack(tag, false, true);  // Requeue

    } catch (Exception e) {
        logger.error("Unknown error");
        channel.basicNack(tag, false, false);
    }
}
```

**Strategy 2: Spring Retry with Exponential Backoff**

```java
@Configuration
public class RetryConfiguration {

    @Bean
    public ListenerContainerCustomizer<MessageListenerContainer>
           containerCustomizer() {
        return container -> {
            container.setDefaultRequeueRejected(false);

            SimpleRetryPolicy retryPolicy = new SimpleRetryPolicy();
            retryPolicy.setMaxAttempts(3);

            ExponentialBackOffPolicy backOffPolicy =
                    new ExponentialBackOffPolicy();
            backOffPolicy.setInitialInterval(1000);
            backOffPolicy.setMultiplier(2.0);

            RetryTemplate retryTemplate = new RetryTemplate();
            retryTemplate.setRetryPolicy(retryPolicy);
            retryTemplate.setBackOffPolicy(backOffPolicy);

            container.setRetryTemplate(retryTemplate);
        };
    }
}

@RabbitListener(queues = "orders")
public void handleOrder(Order order) {
    // Spring retries automatically on exception
    processOrder(order);  // If fails, retried with backoff
}
```

**Strategy 3: Error Handler**

```java
@Configuration
public class ErrorHandlerConfiguration {

    @Bean
    public ListenerContainerCustomizer<MessageListenerContainer>
           errorHandlerCustomizer() {
        return container -> {
            container.setErrorHandler(new ConditionalRejectingErrorHandler(
                new DefaultErrorHandler((message, exception) -> {
                    logger.error("Error handling message: " + exception);

                    // Send to DLQ or store in database
                    deadLetterService.saveForReview(message, exception);
                })
            ));
        };
    }
}
```

**Strategy 4: ExceptionHandlerMethodResolver**

```java
@Component
public class OrderListener {

    @RabbitListener(queues = "orders")
    public void handleOrder(Order order) {
        if (order.isInvalid()) {
            throw new InvalidOrderException("Order validation failed");
        }
        processOrder(order);
    }

    @RabbitExceptionHandler
    public void handleException(InvalidOrderException e,
                                Message message, Channel channel)
                                throws IOException {
        long tag = (long) message.getMessageProperties()
                                .getHeader(AmqpHeaders.DELIVERY_TAG);
        logger.error("Invalid order detected, sending to DLQ");
        channel.basicNack(tag, false, false);  // Send to DLQ
    }
}
```

**Strategy 5: Global Exception Handler with DLQ**

```java
@Configuration
public class GlobalErrorConfig {

    @Bean
    public Queue dlq() {
        return QueueBuilder.durable("global-dlq").build();
    }

    @Bean
    public DirectExchange dlxExchange() {
        return new DirectExchange("dlx");
    }

    @Bean
    public Binding dlqBinding() {
        return BindingBuilder.bind(dlq())
                            .to(dlxExchange())
                            .with("error");
    }

    @Bean
    public MessagePostProcessor dlqErrorPostProcessor() {
        return message -> {
            message.getMessageProperties().setHeader("x-error",
                    "Global error handler");
            return message;
        };
    }
}

@RabbitListener(queues = "global-dlq")
public void handleDLQ(Message message) {
    logger.error("Message in DLQ: " +
                new String(message.getBody()));
    // Manual review, fix, and republish
}
```

---

### Q27. How do you test RabbitMQ listeners in Spring Boot?

**Answer:**

**Setup with TestContainers (Recommended):**

```xml
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>testcontainers</artifactId>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>rabbitmq</artifactId>
    <scope>test</scope>
</dependency>
```

**Test Class:**

```java
@SpringBootTest
@Testcontainers
public class OrderConsumerTest {

    @Container
    static RabbitMQContainer rabbitMQ =
            new RabbitMQContainer("rabbitmq:latest");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.rabbitmq.host", rabbitMQ::getHost);
        registry.add("spring.rabbitmq.port", rabbitMQ::getAmqpPort);
        registry.add("spring.rabbitmq.username", () -> "guest");
        registry.add("spring.rabbitmq.password", () -> "guest");
    }

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Autowired
    private OrderService orderService;

    private static CountDownLatch latch;

    @Test
    public void testOrderProcessing() throws InterruptedException {
        // Arrange
        Order order = new Order("ORD123", 100.0);
        latch = new CountDownLatch(1);

        // Act
        rabbitTemplate.convertAndSend("orders", "order.created", order);

        // Assert
        boolean received = latch.await(10, TimeUnit.SECONDS);
        assertTrue(received, "Order not processed within timeout");

        Order saved = orderService.findById("ORD123");
        assertNotNull(saved);
        assertEquals(100.0, saved.getAmount());
    }
}
```

**With Awaility (Better Assertions):**

```java
@SpringBootTest
@Testcontainers
public class OrderConsumerAwailityTest {

    @Container
    static RabbitMQContainer rabbitMQ =
            new RabbitMQContainer("rabbitmq:latest");

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.rabbitmq.host", rabbitMQ::getHost);
        registry.add("spring.rabbitmq.port", rabbitMQ::getAmqpPort);
    }

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Autowired
    private OrderRepository orderRepository;

    @Test
    public void testOrderPersistence() {
        // Arrange
        Order order = new Order("ORD123", 100.0);

        // Act
        rabbitTemplate.convertAndSend("orders", "order.created", order);

        // Assert using Awaitility
        await()
            .pollInterval(100, MILLISECONDS)
            .atMost(5, SECONDS)
            .untilAsserted(() -> {
                Order saved = orderRepository.findById("ORD123").orElse(null);
                assertNotNull(saved);
                assertEquals(100.0, saved.getAmount());
            });
    }
}
```

**Mock Consumer Test:**

```java
@SpringBootTest
public class OrderProducerTest {

    @MockBean
    private OrderConsumer orderConsumer;

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void testMessagePublishing() throws InterruptedException {
        // Arrange
        Order order = new Order("ORD123", 100.0);
        ArgumentCaptor<Order> captor = ArgumentCaptor.forClass(Order.class);

        // Act
        rabbitTemplate.convertAndSend("orders", "order.created", order);

        // Assert
        Thread.sleep(1000);  // Give listener time to process

        verify(orderConsumer, times(1)).handleOrder(captor.capture());
        assertEquals("ORD123", captor.getValue().getId());
    }
}
```

---

## **SECTION 6: ADVANCED & REAL-WORLD (Q28-Q30)**

### Q28. What are best practices for RabbitMQ in production?

**Answer:**

**1. Connection & Channel Management**

```java
@Configuration
public class ProductionRabbitConfig {

    @Bean
    public ConnectionFactory connectionFactory() {
        CachingConnectionFactory factory =
                new CachingConnectionFactory("rabbitmq-host");
        factory.setUsername("secure-user");
        factory.setPassword("secure-password");

        // Connection pooling
        factory.setCacheMode(CachingConnectionFactory.CacheMode.CHANNEL);
        factory.setChannelCacheSize(25);  // Tuned for workload
        factory.setConnectionCacheSize(1);  // 1 connection, multiple channels

        return factory;
    }
}
```

**2. Durable Infrastructure**

```java
@Configuration
public class DurableInfrastructure {

    @Bean
    public Queue orderQueue() {
        return QueueBuilder.durable("orders")
                          .withArgument("x-queue-type", "quorum")  // HA
                          .withArgument("x-quorum-initial-group-size", 3)
                          .deadLetterExchange("dlx")  // Error handling
                          .build();
    }

    @Bean
    public TopicExchange orderExchange() {
        return ExchangeBuilder.topicExchange("orders")
                             .durable(true)
                             .build();
    }
}
```

**3. Message Persistence & Acknowledgment**

```java
@Service
public class ReliableOrderService {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void sendOrder(Order order) {
        rabbitTemplate.convertAndSend("orders", "order.created", order, msg -> {
            // Persistent message
            msg.getMessageProperties()
                .setDeliveryMode(MessageDeliveryMode.PERSISTENT);
            // Add timeout
            msg.getMessageProperties().setExpiration("3600000");
            // Add correlation ID for tracing
            msg.getMessageProperties().setCorrelationId(order.getTrackingId());
            return msg;
        });
    }
}

@RabbitListener(queues = "orders", ackMode = AcknowledgeMode.MANUAL)
public void processOrder(Order order, Channel channel,
                        @Header(AmqpHeaders.DELIVERY_TAG) long tag)
                        throws IOException {
    try {
        validateAndSaveOrder(order);
        channel.basicAck(tag, false);  // Explicit ACK
    } catch (Exception e) {
        logger.error("Failed to process: " + order.getId(), e);
        channel.basicNack(tag, false, false);  // Send to DLQ
    }
}
```

**4. Monitoring & Logging**

```java
@Configuration
public class MonitoringConfig {

    @Bean
    public PropertiesConfigurer configurer() {
        return new PropertiesConfigurer();
    }

    @Bean
    public SimpleMessageListenerContainer container(
            ConnectionFactory connectionFactory) {
        SimpleMessageListenerContainer container =
                new SimpleMessageListenerContainer(connectionFactory);

        // Monitoring
        container.setAdviceChain(new MethodInterceptor[]{
            (invocation) -> {
                long startTime = System.currentTimeMillis();
                try {
                    return invocation.proceed();
                } finally {
                    long duration = System.currentTimeMillis() - startTime;
                    logger.info("Message processing took: " + duration + "ms");
                }
            }
        });

        return container;
    }
}
```

**5. Error Handling & Retry**

```java
@Configuration
public class ProductionErrorHandling {

    @Bean
    public ListenerContainerCustomizer<MessageListenerContainer>
           errorHandlerCustomizer() {
        return container -> {
            SimpleRetryPolicy retryPolicy = new SimpleRetryPolicy();
            retryPolicy.setMaxAttempts(5);
            retryPolicy.setTraverseCauses(true);

            ExponentialBackOffPolicy backOffPolicy =
                    new ExponentialBackOffPolicy();
            backOffPolicy.setInitialInterval(1000);
            backOffPolicy.setMultiplier(2.0);
            backOffPolicy.setMaxInterval(60000);  // Max 1 minute wait

            RetryTemplate retryTemplate = new RetryTemplate();
            retryTemplate.setRetryPolicy(retryPolicy);
            retryTemplate.setBackOffPolicy(backOffPolicy);

            ConditionalRejectingErrorHandler errorHandler =
                    new ConditionalRejectingErrorHandler(
                        new DefaultErrorHandler(retryTemplate)
                    );
            container.setErrorHandler(errorHandler);
        };
    }
}
```

**6. Performance Tuning**

```java
application.properties:

# Connection
spring.rabbitmq.connection-factory.cache-mode=CHANNEL
spring.rabbitmq.connection-factory.channel-cache-size=50

# Listener
spring.rabbitmq.listener.simple.concurrency=10
spring.rabbitmq.listener.simple.max-concurrency=20
spring.rabbitmq.listener.simple.prefetch=10
spring.rabbitmq.listener.simple.default-requeue-rejected=false
spring.rabbitmq.listener.simple.acknowledge-mode=MANUAL
spring.rabbitmq.listener.simple.retry.enabled=true
spring.rabbitmq.listener.simple.retry.initial-interval=1000
spring.rabbitmq.listener.simple.retry.max-attempts=5

# SSL
spring.rabbitmq.ssl.enabled=true
spring.rabbitmq.ssl.key-store=classpath:keystore.jks
spring.rabbitmq.ssl.key-store-password=password
```

**Production Checklist:**

- ✅ Use Quorum queues for HA
- ✅ Enable message persistence
- ✅ Manual acknowledgment for critical operations
- ✅ Implement DLQ for error handling
- ✅ Set up monitoring and alerting
- ✅ Configure retry with backoff
- ✅ Use SSL/TLS for connections
- ✅ Implement circuit breakers
- ✅ Add distributed tracing (correlation IDs)
- ✅ Test failover scenarios

---

### Q29. How do you implement request-reply (RPC) pattern in RabbitMQ?

**Answer:**

**RPC (Remote Procedure Call) Pattern:**

- Client sends request and waits for response
- Server receives request, processes, and replies
- Useful for synchronous operations over async messaging

**Implementation:**

```java
// Server (Responder)
@RabbitListener(queues = "rpc-queue")
public String handleRPC(String request) {
    System.out.println("Processing RPC request: " + request);
    return "Response to: " + request;
}

// Client (Requester)
@Service
public class RPCClient {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public String sendRPC(String request) {
        // Send request, wait for reply
        String response = (String) rabbitTemplate.convertSendAndReceive(
            "rpc-exchange",
            "rpc-key",
            request
        );
        return response;
    }
}
```

**Complete Example with Configuration:**

```java
@Configuration
public class RPCConfiguration {

    static final String RPC_QUEUE = "rpc-queue";
    static final String RPC_EXCHANGE = "rpc-exchange";

    // Server setup
    @Bean
    public Queue rpcQueue() {
        return new Queue(RPC_QUEUE);
    }

    @Bean
    public TopicExchange rpcExchange() {
        return new TopicExchange(RPC_EXCHANGE);
    }

    @Bean
    public Binding rpcBinding() {
        return BindingBuilder.bind(rpcQueue())
                            .to(rpcExchange())
                            .with("rpc.*");
    }
}

// RPC Server
@Service
public class OrderRPCServer {

    @RabbitListener(queues = "rpc-queue")
    public OrderResponse handleRPC(OrderRequest request) {
        System.out.println("Received RPC request: " + request.getOrderId());

        // Process order synchronously
        Order order = findOrder(request.getOrderId());

        return OrderResponse.builder()
                          .orderId(order.getId())
                          .status(order.getStatus())
                          .totalAmount(order.getAmount())
                          .build();
    }
}

// RPC Client
@Service
public class OrderRPCClient {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public OrderResponse getOrderStatus(String orderId) {
        OrderRequest request = new OrderRequest(orderId);

        // Synchronous call - waits for response
        OrderResponse response = (OrderResponse)
            rabbitTemplate.convertSendAndReceive(
                "rpc-exchange",
                "rpc.status",
                request,
                message -> {
                    message.getMessageProperties()
                          .setReplyTo("reply-queue");
                    message.getMessageProperties()
                          .setCorrelationId(UUID.randomUUID().toString());
                    return message;
                }
            );

        return response;
    }
}
```

**With Timeout:**

```java
public OrderResponse getOrderStatusWithTimeout(String orderId) {
    OrderRequest request = new OrderRequest(orderId);

    // Timeout if server doesn't respond in 5 seconds
    ReceiveAndConvertSpec spec =
        rabbitTemplate.convertSendAndReceive(
            "rpc-exchange",
            "rpc.status",
            request
        );

    if (spec == null) {
        throw new TimeoutException("RPC server did not respond");
    }

    return (OrderResponse) spec;
}
```

**When to Use RPC:**

- ✅ Synchronous operations needed
- ✅ Need immediate response
- ❌ For high-latency, fire-and-forget scenarios
- ❌ When async would be better (orders, events)

---

### Q30. How do you implement distributed tracing with correlation IDs in RabbitMQ?

**Answer:**

**Correlation ID Pattern:** Track messages through multiple services for debugging.

**Setup:**

```java
// Utility class for correlation ID
@Component
public class CorrelationIdManager {

    private static final ThreadLocal<String> CORRELATION_ID =
            new ThreadLocal<>();

    public static String getCorrelationId() {
        String id = CORRELATION_ID.get();
        if (id == null) {
            id = UUID.randomUUID().toString();
            CORRELATION_ID.set(id);
        }
        return id;
    }

    public static void setCorrelationId(String id) {
        CORRELATION_ID.set(id);
    }

    public static void clear() {
        CORRELATION_ID.remove();
    }
}

// Producer: Create and attach correlation ID
@Service
public class OrderProducer {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void publishOrder(Order order) {
        String correlationId = CorrelationIdManager.getCorrelationId();

        rabbitTemplate.convertAndSend("orders", "order.created", order, msg -> {
            msg.getMessageProperties().setCorrelationId(correlationId);
            msg.getMessageProperties().setDeliveryMode(
                MessageDeliveryMode.PERSISTENT
            );
            return msg;
        });

        logger.info("Order published with correlation ID: " + correlationId);
    }
}

// Consumer 1: Log with correlation ID
@RabbitListener(queues = "orders")
public void processOrder(Order order,
                        @Header("correlation-id") String correlationId) {
    CorrelationIdManager.setCorrelationId(correlationId);

    try {
        logger.info("[{}] Processing order: {}", correlationId, order.getId());
        validateOrder(order);
        saveOrder(order);
        logger.info("[{}] Order saved successfully", correlationId);

        // Publish event for next service
        publishOrderCreatedEvent(order, correlationId);

    } finally {
        CorrelationIdManager.clear();
    }
}

// Consumer 2: Email service
@RabbitListener(queues = "email-queue")
public void sendOrderConfirmationEmail(Order order,
                                      @Header("correlation-id") String correlationId) {
    CorrelationIdManager.setCorrelationId(correlationId);

    try {
        logger.info("[{}] Sending confirmation email", correlationId);
        emailService.sendOrderConfirmation(order);
        logger.info("[{}] Email sent", correlationId);
    } finally {
        CorrelationIdManager.clear();
    }
}

// Consumer 3: SMS service
@RabbitListener(queues = "sms-queue")
public void sendOrderNotificationSMS(Order order,
                                    @Header("correlation-id") String correlationId) {
    CorrelationIdManager.setCorrelationId(correlationId);

    try {
        logger.info("[{}] Sending SMS notification", correlationId);
        smsService.sendNotification(order.getPhone(), "Order confirmed");
        logger.info("[{}] SMS sent", correlationId);
    } finally {
        CorrelationIdManager.clear();
    }
}

// With SLF4J MDC for automatic logging
@Component
public class CorrelationIdInterceptor {

    @Bean
    public ListenerContainerCustomizer<MessageListenerContainer>
           containerCustomizer() {
        return container -> {
            container.setAdviceChain(new MethodInterceptor[]{
                (invocation) -> {
                    Message message = (Message) invocation.getArguments()[1];
                    String correlationId = (String)
                        message.getMessageProperties().getHeader("correlation-id");

                    if (correlationId != null) {
                        MDC.put("correlationId", correlationId);
                    }

                    try {
                        return invocation.proceed();
                    } finally {
                        MDC.remove("correlationId");
                    }
                }
            });
        };
    }
}

// Logging configuration (logback.xml)
// <pattern>%d{HH:mm:ss.SSS} [%X{correlationId}] %-5level %logger{36} - %msg%n</pattern>
// Output: 15:30:45.123 [550e8400-e29b-41d4-a716-446655440000] INFO com.example.OrderProducer
```

**With Distributed Tracing (Sleuth + Zipkin):**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

```properties
spring.zipkin.base-url=http://localhost:9411
spring.sleuth.sampler.probability=1.0
```

```java
// Spring Cloud Sleuth handles correlation automatically
@Service
public class OrderServiceWithSleuth {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Autowired
    private Tracer tracer;

    public void processOrder(Order order) {
        // Sleuth automatically adds trace/span IDs to RabbitMQ headers
        rabbitTemplate.convertAndSend("orders", "order.created", order);

        // All logs automatically include trace ID
        logger.info("Order sent: " + order.getId());
        // Output: [service-name,550e8400-e29b-41d4-a716-446655440000,...]
    }
}
```

**Benefits:**

- ✅ Track message through multiple services
- ✅ Debug failures end-to-end
- ✅ Performance monitoring (timing per service)
- ✅ Visualize service interactions

---

## **Summary Table: Quick Reference**

| Topic          | Key Concept                          | Configuration                     |
| -------------- | ------------------------------------ | --------------------------------- |
| Queues         | Durable, Auto-delete, Exclusive, TTL | `QueueBuilder`                    |
| Exchanges      | Direct, Topic, Fanout, Headers       | `ExchangeBuilder`                 |
| Acknowledgment | Auto, Manual, None                   | `ackMode`                         |
| Prefetch       | Message batch size                   | `setPrefetchCount()`              |
| Persistence    | Durable queue + persistent message   | Queue durable + `PERSISTENT` mode |
| HA             | Quorum queues (preferred)            | `x-queue-type: quorum`            |
| DLQ            | Dead letter exchange binding         | `deadLetterExchange()`            |
| Retry          | Exponential backoff                  | `RetryTemplate`                   |
| RPC            | Request-reply pattern                | `convertSendAndReceive()`         |
| Tracing        | Correlation IDs                      | `CorrelationIdManager`            |

---

**Good luck with your interviews! 🚀**
