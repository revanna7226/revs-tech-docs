# Commands to Install and Run Zookeeper/Kafka Broker

1. Download Apache Kafka binary file from [Official Page](https://kafka.apache.org/downloads).
2. Extract the binary file into your local machine.
3. Execute the following command inside the root directory to run Zookeeper.

   ```bash
   sh bin/zookeeper-server-start.sh config/zookeeper.properties
   ```

   :::{note}
   Zookeeper will up and running on 0.0.0.0:2181.
   :::

4. Execute the below command to start Kafka Server.
   ```bash
   sh bin/kafka-server-start.sh config/server.properties
   ```
   :::{note}
   Kafka Server will up and running on revs.localdomain:9092.
   :::
5. Create a topic.
