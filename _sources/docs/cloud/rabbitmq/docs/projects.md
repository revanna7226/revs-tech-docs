# Projects

## Maven Project to publish message to one of the Queue

Dependency

```xml
<!-- https://mvnrepository.com/artifact/com.rabbitmq/amqp-client -->
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.28.0</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.json/json -->
<dependency>
    <groupId>org.json</groupId>
    <artifactId>json</artifactId>
    <version>20250517</version>
</dependency>
```

### Publisher

```java
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Publisher {
    public void publish(String message, String queueName) throws IOException, TimeoutException {
        // Implementation for publishing a message to the specified queue

        ConnectionFactory connectionFactory = new ConnectionFactory();
        Connection connection = connectionFactory.newConnection();
        System.out.println("Connection established: " + connection.getClientProvidedName());

        Channel channel = connection.createChannel();
        System.out.println("Channel created: " + channel.getChannelNumber());

        channel.basicPublish("", queueName, null, message.getBytes());
        System.out.println("Message published to 'Queue1'");

        channel.close();
        connection.close();
    }

    public static void main(String[] args) {
        Publisher publisher = new Publisher();
        try {
            publisher.publish("Hello, World1 " + System.currentTimeMillis(), "Queue1");
        } catch (IOException | TimeoutException e) {
            e.printStackTrace();
        }
    }
}
```

### Consumer

```java
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Consumer {
    public void accept(String message) throws IOException, TimeoutException {
        ConnectionFactory factory = new ConnectionFactory();
        Connection connection = factory.newConnection();
        System.out.println("Connection established: " + connection.getClientProvidedName());

        Channel channel = connection.createChannel();
        System.out.println("Channel created: " + channel.getChannelNumber());

        channel.basicConsume("Queue1", true, (consumerTag, delivery) -> {
            String receivedMessage = new String(delivery.getBody(), "UTF-8");
            System.out.println("Received message: " + receivedMessage);
        }, consumerTag -> {});
    }

    public static void main(String[] args) {
        Consumer consumer = new Consumer();
        try {
            consumer.accept("Listening for messages...");
        } catch (IOException | TimeoutException e) {
            e.printStackTrace();
        }
    }
}

```

## Purging Messages

Purging a queue removes ALL messages from that queue, but:

- The queue remains
- Messages are permanently deleted
- Consumers do NOT receive them
  This cannot be undone

```bash
#Purge a specific queue
rabbitmqctl purge_queue Queue1

#Purge queue in a virtual host
rabbitmqctl -p /myvhost purge_queue Queue1
```

```java
channel.queuePurge("Queue1");
```
