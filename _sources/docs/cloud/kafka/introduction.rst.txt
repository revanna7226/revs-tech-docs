Introduction 
===============

Definition:
    Apache Kafka is a distributed event streaming platform designed to handle large volumes of real-time data.

.. image:: https://www.tutorialspoint.com/apache_kafka/images/fundamentals.jpg
   :align: center
   :alt: Apache Kafka Cluster Architecture

Here is how it works simply:
    - Producers (applications or systems) send messages (data) to Kafka.
    - Kafka organizes these messages into topics, which are categories or feeds for different types of data.
    - Topics are split into partitions to allow the data to be processed in parallel for high scalability.
    - Consumers subscribe to these topics to read the messages when they need them.
    - Kafka stores the data reliably and allows consumers to read messages at their own pace, even re-reading older data if required.

Kafka is widely used in industry for building real-time analytics, event-driven applications, monitoring systems, and data pipelines at companies like LinkedIn, Netflix, Uber, and Airbnb.

Key Kafka Terminologies:
    #. Topic: A named stream to which records (messages) are sent by producers and read by consumers.
    #. Partition: A topic is split into partitions for scalability and parallelism; each partition is an ordered, immutable sequence of records.
    #. Broker: A Kafka server that stores data and serves client requests; a cluster contains multiple brokers.
    #. Producer: Publishes records to Kafka topics.
    #. Consumer: Subscribes to topics and reads records for processing.
    #. Consumer Group: A collection of consumers sharing a group ID; Kafka delivers messages among them for load balancing.
    #. Offset: The position of a record within a partition; used for tracking consumption progress.

Kafka APIs:
    .. list-table::
        :header-rows: 1
        :widths: 20 40 40

        * - API Name
          - Purpose/Description
          - Typical Use Case
        * - Producer API
          - Sends (publishes) records to Kafka topics, handles serialization, partitioning, reliability, and batching.
          - Real-time data ingestion, event streaming
        * - Consumer API
          - Reads records from topics, manages subscriptions, consumer groups, and offset tracking.
          - Building scalable processing apps
        * - Streams API
          - Processes data streams directly within Kafka, supports transformation and aggregation.
          - Real-time analytics, aggregation
        * - Connect API
          - Connects Kafka to external systems (databases, queues, storage) via connectors—no custom code needed.
          - Integrating and synchronizing data
        * - Admin API
          - Programmatic creation, deletion, and inspection of topics and other resources, plus configuration management.
          - Automating and managing Kafka infrastructure


Some Useful Links:
    - `Apache Kafka - Fundamentals <https://www.tutorialspoint.com/apache_kafka/apache_kafka_fundamentals.htm>`_