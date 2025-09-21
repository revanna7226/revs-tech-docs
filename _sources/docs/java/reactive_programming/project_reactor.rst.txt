What is Project Reactor?
+++++++++++++++++++++++++++++++++++++++

**Project Reactor** is a reactive programming library for Java, developed by **Pivotal** (now part of VMware).

It provides an implementation of the **Reactive Streams specification** and serves as the foundation for **Spring WebFlux**.

It enables developers to build **asynchronous, non-blocking, event-driven applications** that can handle large amounts of data efficiently.


**Useful Links**

- `Project Reactor Official Website <https://projectreactor.io>`_
- `Reactor 3 Reference Guide <https://docs.spring.io/projectreactor/reactor-core/docs/3.3.22.RELEASE/reference/html/#about-doc>`_
- `Java Doc for Reactor Core <https://projectreactor.io/docs/core/release/api/overview-summary.html>`_

Key Features of Project reactor
-------------------------------------

**Reactive Streams Compliant**

* Implements ``Publisher``, ``Subscriber``, ``Subscription``, and ``Processor``.
* Supports **backpressure** (consumer controls the rate of data flow).

**Two Main Types**

* **Flux<T>** → stream of **0..N items** (like an async list).
* **Mono<T>** → stream of **0..1 item** (like an async optional).

**Functional API**

* Uses operators (``map``, ``filter``, ``flatMap``, etc.) similar to Java Streams but asynchronous.

**Backpressure Support**

* Prevents overwhelming consumers by controlling demand.

**Integration**

* Forms the basis of **Spring WebFlux** (reactive alternative to Spring MVC).
* Works with messaging systems (Kafka, RabbitMQ), databases (R2DBC), and cloud services.

Core Interfaces in Project Reactor
===========================================

Project Reactor implements the **Reactive Streams specification**  
(introduced in Java 9 as ``java.util.concurrent.Flow``),  
based on the model:

**Publisher → Subscriber → Subscription → Processor**

----

Publisher<T>
------------------------------

* From ``org.reactivestreams.Publisher<T>``.
* Represents a provider of a potentially unbounded number of elements.
* A ``Publisher`` emits data to a ``Subscriber``.
* In Reactor, ``Flux<T>`` and ``Mono<T>`` are concrete implementations.

Example::

   Flux<Integer> flux = Flux.range(1, 5); // Publisher

----

Subscriber<T>
------------------------------

* From ``org.reactivestreams.Subscriber<T>``.
* Consumes data emitted by a ``Publisher``.
* Has 4 callback methods:

  * ``onSubscribe(Subscription s)`` → called when subscription is created.
  * ``onNext(T item)`` → called for each emitted item.
  * ``onError(Throwable t)`` → called if an error occurs.
  * ``onComplete()`` → called when the Publisher completes successfully.

Example::

   flux.subscribe(
       data -> System.out.println("Received: " + data),   // onNext
       err -> System.err.println("Error: " + err),        // onError
       () -> System.out.println("Completed!")             // onComplete
   );

----

subscription
------------------------------

* From ``org.reactivestreams.Subscription``.
* Represents the link between Publisher and Subscriber.
* Used to control demand (backpressure).

Key methods:

* ``request(long n)`` → request *n* items from the Publisher.
* ``cancel()`` → cancel the subscription.

Example::

   flux.subscribe(new Subscriber<Integer>() {
       private Subscription subscription;
       
       @Override
       public void onSubscribe(Subscription s) {
           this.subscription = s;
           s.request(2); // request only 2 items at first
       }

       @Override
       public void onNext(Integer item) {
           System.out.println("Got: " + item);
           subscription.request(1); // request more items dynamically
       }

       @Override
       public void onError(Throwable t) {
           System.err.println("Error: " + t);
       }

       @Override
       public void onComplete() {
           System.out.println("Done!");
       }
   });

----

Processor<T, R>
------------------------------

* From ``org.reactivestreams.Processor<T,R>``.
* Acts as both a **Subscriber** (to receive data) and a **Publisher** (to emit transformed data).
* In Reactor, ``DirectProcessor`` and ``EmitterProcessor`` are examples.
* Useful for bridging or adapting data streams.

Example::

   DirectProcessor<String> processor = DirectProcessor.create();

   processor.subscribe(data -> System.out.println("Subscriber 1: " + data));
   processor.subscribe(data -> System.out.println("Subscriber 2: " + data));

   processor.onNext("Hello");
   processor.onNext("Reactive World");
   processor.onComplete();

----

Reactor Specific Interfaces
----------------------------------------    

Apart from Reactive Streams standard ones, Reactor adds:

* **Flux<T>**
  
  - Represents a ``Publisher`` that can emit **0..N items**.
  - Similar to a ``List`` but asynchronous and stream-based.

* **Mono<T>**

  - Represents a ``Publisher`` that emits **0..1 item**.
  - Useful for single-value async responses (like DB query, HTTP call).

* **Schedulers**

  - Control execution context (thread pools).
  - Common ones:

    - ``Schedulers.parallel()``
    - ``Schedulers.boundedElastic()``
    - ``Schedulers.immediate()``

----

Summary
-----------------------------

.. list-table:: Project Reactor Interfaces
   :header-rows: 1
   :widths: 20 50

   * - Interface
     - Responsibility
   * - **Publisher**
     - Emits data (``Flux``, ``Mono``).
   * - **Subscriber**
     - Consumes data.
   * - **Subscription**
     - Controls demand (``request()``, ``cancel()``).
   * - **Processor**
     - Acts as both Publisher & Subscriber.
   * - **Flux & Mono**
     - Reactor-specific types for multi-value and single-value streams.

