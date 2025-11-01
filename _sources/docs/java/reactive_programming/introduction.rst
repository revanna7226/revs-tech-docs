Reactive Programming in Java
+++++++++++++++++++++++++++++++++++++++

What is Reactive Programming?
=============================

**Reactive Programming** is a paradigm focused on building
**asynchronous, non-blocking, event-driven** applications
that can handle **large volumes of data streams** efficiently.

Instead of writing code that *pulls* data (imperative style),
reactive code is designed to *react* to data as it arrives (push-based).

In Java, Reactive Programming is usually implemented using:

- **Project Reactor** (part of Spring ecosystem)
- **RxJava**
- **Akka Streams**
- **Vert.x**

Key Concepts
---------------------

1. **Asynchronous & Non-blocking**

   - Code does not wait for an operation (like I/O or DB calls) to finish.
   - Threads are freed up to do other work while the operation completes.

2. **Data Streams**

   - Everything is treated as a stream of events/data (mouse clicks, DB results, HTTP responses, etc.).

3. **Backpressure**

   - A mechanism to handle producers that generate data faster than consumers can process.
   - Prevents memory overflow and improves stability.

4. **Publisher-Subscriber Model (Reactive Streams API in Java 9)**

   - *Publisher* → produces data
   - *Subscriber* → consumes data
   - *Subscription* → manages demand (request/cancel)
   - *Processor* → acts as both Publisher & Subscriber

Why Do We Need Reactive Programming?
====================================

1. **Scalability & Performance**

   - Traditional blocking I/O (Servlet model) ties up a thread per request.
   - Reactive programming uses fewer threads with async/non-blocking I/O,
     allowing thousands of concurrent users efficiently.

2. **Efficient Resource Utilization**

   - Threads are expensive (memory + context switching).
   - Reactive style minimizes idle waiting threads → better CPU & memory usage.

3. **Resilience**

   - Reactive systems often use event-driven & message-driven patterns.
   - Failures are isolated and easier to recover from.

4. **Real-time Applications**

   - Perfect for apps where **data is continuously streamed** and services must **react instantly**.

     * Chat apps
     * Stock market dashboards
     * IoT systems
     * Streaming services

5. **Cloud-Native & Microservices**

   - In distributed systems, latency and async communication are common.
   - Reactive programming makes handling async service calls (HTTP, DB, Kafka, etc.) scalable.

Example in Java (Project Reactor)
----------------------------------------------

.. code-block:: java

   import reactor.core.publisher.Flux;

   public class ReactiveDemo {
       public static void main(String[] args) {
           // A stream of numbers
           Flux<Integer> numbers = Flux.range(1, 5);

           // Reacting to the stream asynchronously
           numbers
               .map(n -> n * 2)           // transformation
               .filter(n -> n % 4 == 0)   // filtering
               .subscribe(
                   data -> System.out.println("Received: " + data),   // onNext
                   error -> System.err.println("Error: " + error),    // onError
                   () -> System.out.println("Stream completed!")      // onComplete
               );
       }
   }

Output
--------------- 

.. code-block:: text

   Received: 4
   Received: 8
   Stream completed!

Reactive vs Imperative (Comparison)
----------------------------------------

.. list-table:: Reactive vs Imperative Programming in Java
   :header-rows: 1
   :widths: 30 35 35

   * - Aspect
     - Imperative (Traditional Java)
     - Reactive (Project Reactor / RxJava)
   * - Programming Model
     - Blocking, request-per-thread model
     - Non-blocking, event-driven, async streams
   * - Concurrency
     - Each request consumes a thread until complete
     - Fewer threads handle many requests concurrently
   * - Resource Usage
     - High thread & memory usage under load
     - Efficient CPU & memory utilization
   * - Latency
     - Higher latency due to blocking I/O
     - Lower latency via async pipelines
   * - Error Handling
     - Try-catch in sequential flow
     - Error callbacks (`onError`) in stream
   * - Use Cases
     - Small apps, batch jobs, synchronous APIs
     - High-concurrency, real-time apps, microservices

Summary
------------------------------

- Reactive Programming in Java is **asynchronous, non-blocking, and event-driven**.
- Needed for **scalable, resilient, and real-time applications**.
- Especially valuable in **cloud, microservices, and high-concurrency environments**.
