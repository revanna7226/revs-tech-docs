# Java Questions

## Core Java

1. Describe Java access modifiers.
1. What is the difference between package private and protected scope?
1. How do you prevent a class from being extended?
1. What is the difference between an abstract class and an interface?

### When would you use an abstract class instead of an interface?

| **Criteria**                             | **Abstract Class**                                                      | **Interface**                                                                                           |
| ---------------------------------------- | ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| **Purpose**                              | Used when classes share a **common base behavior** or state             | Used to define a **contract** or capability that can be applied to any class                            |
| **Keyword**                              | Declared using `abstract` keyword                                       | Declared using `interface` keyword                                                                      |
| **Method Implementation**                | Can have both **abstract** and **non-abstract** (implemented) methods   | Before Java 8 – only abstract methods; from Java 8 onward – can have **default** and **static** methods |
| **State (Fields)**                       | Can have **instance variables**, constants, and static fields           | Can only have **public static final** constants                                                         |
| **Constructors**                         | Can have constructors                                                   | Cannot have constructors                                                                                |
| **Access Modifiers**                     | Methods can have any access modifier (`public`, `protected`, `private`) | All methods are **public** by default                                                                   |
| **Multiple Inheritance**                 | A class can extend **only one** abstract class                          | A class can implement **multiple** interfaces                                                           |
| **When to Use**                          | When you want to share **common code or state** among related classes   | When you want to define a **contract or capability** that can be shared across **unrelated classes**    |
| **Example Use Case**                     | Base class `Shape` with shared fields like `color` and method `area()`  | Interface `Drawable` or `Serializable` that defines behavior without implementation                     |
| **Introduced Default Methods (Java 8+)** | Already supported concrete methods                                      | Introduced `default` and `static` methods for partial implementation                                    |
| **Performance**                          | Slightly faster – supports direct inheritance                           | Requires indirection due to multiple implementations                                                    |
| **Supports Variables (Stateful)**        | Yes, can maintain instance state                                        | No, stateless (except constants)                                                                        |

✅ Use an Abstract Class when:

- You want to share code between closely related classes.
- You need to define common state (fields) or constructors.
- You want to provide base functionality that subclasses can reuse or override.
- You expect subclasses to have a common identity (“is-a” relationship).

```java
public abstract class Animal {
    String name;

    public Animal(String name) {
        this.name = name;
    }

    public abstract void makeSound();

    public void eat() {
        System.out.println(name + " is eating.");
    }
}

public class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }

    @Override
    public void makeSound() {
        System.out.println("Woof!");
    }
}

```

✅ Use an Interface when:

- You want to define a contract or capability that can be shared across unrelated classes.
- You want multiple inheritance of type (one class can implement many interfaces).
- You don’t need shared state — only method signatures or default behaviors.

```java
public interface Flyable {
    void fly();
}

public class Bird implements Flyable {
    @Override
    public void fly() {
        System.out.println("Bird is flying!");
    }
}

public class Airplane implements Flyable {
    @Override
    public void fly() {
        System.out.println("Airplane is flying!");
    }
}

```

### What is the difference between a checked and unchecked exceptions?

| **Criteria**              | **Checked Exceptions**                                                                               | **Unchecked Exceptions**                                                                                    |
| ------------------------- | ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **Definition**            | Exceptions that are **checked at compile-time** by the Java compiler.                                | Exceptions that are **not checked at compile-time**, they occur at **runtime**.                             |
| **Superclass**            | Subclasses of `Exception` _(excluding `RuntimeException`)_                                           | Subclasses of `RuntimeException`                                                                            |
| **Compile-Time Checking** | Must be either **handled using `try-catch`** or **declared using `throws`** in the method signature. | Compiler does **not force** you to handle or declare them.                                                  |
| **Occurrence**            | Generally caused by **external factors** (e.g., file not found, network errors).                     | Usually caused by **programming mistakes** (e.g., null pointer, division by zero).                          |
| **Examples**              | `IOException`, `SQLException`, `FileNotFoundException`, `ClassNotFoundException`                     | `NullPointerException`, `ArithmeticException`, `ArrayIndexOutOfBoundsException`, `IllegalArgumentException` |
| **Handling Requirement**  | **Mandatory** to handle or declare.                                                                  | **Optional** to handle.                                                                                     |

### When would use an unchecked exception?

You should use an unchecked exception (i.e., a subclass of RuntimeException) when the error is caused by a programming mistake that can and should be prevented through better code logic — not something the caller is expected to recover from.

Example:

```java
public class BankAccount {
    private double balance;

    public void withdraw(double amount) {
        if (amount < 0) {
            throw new IllegalArgumentException("Amount cannot be negative");
        }
        if (amount > balance) {
            throw new RuntimeException("Insufficient funds");
        }
        balance -= amount;
    }
}

```

- Here, both exceptions are unchecked.
- They represent programming or input logic errors, not conditions that can be “handled” meaningfully by the caller.

### Describe Object's hashCode and equals contract.

In Java, every class inherits two important methods from the Object class:

```java
public boolean equals(Object obj)

public int hashCode()
```

These two methods work together and must obey a specific contract to ensure consistent behavior — especially when objects are stored in hash-based collections like `HashMap`, `HashSet`, or `Hashtable`.

**The equals() Contract:**
The equals() method defines how two objects are considered equal.

It must satisfy the following five conditions:
| **Property** | **Description** |
| -------------- | --------------------------------------------------------------------------------------------------------------------- |
| **Reflexive** | For any object `x`, `x.equals(x)` must be `true`. |
| **Symmetric** | For any objects `x` and `y`, if `x.equals(y)` is `true`, then `y.equals(x)` must also be `true`. |
| **Transitive** | For any objects `x`, `y`, `z`, if `x.equals(y)` and `y.equals(z)` are `true`, then `x.equals(z)` must also be `true`. |
| **Consistent** | Repeated calls to `x.equals(y)` must consistently return the same result (unless objects change). |
| **Non-null** | For any non-null reference `x`, `x.equals(null)` must return `false`. |

**The hashCode() Contract:**
The hashCode() method returns an integer hash value for the object.
It must obey the following rules:
| **Rule** | **Description** |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| **Consistent with equals** | If two objects are _equal_ according to `equals()`, they **must** have the same `hashCode()`. |
| **Not required to be unique** | If two objects are _not equal_, they **can** have the same hash code (but it’s better if they don’t — for performance). |
| **Stable** | The hash code must remain the same as long as the object’s state (used in `equals`) does not change. |

Hash-based collections (like HashSet, HashMap) use the hash code to find a bucket, and then use equals() to check equality.

If the contract is violated:

- Equal objects may end up in different buckets, causing lookup failures.
- Collections may lose elements or behave unpredictably.

Example:

```java
import java.util.Objects;

public class Employee {
    private int id;
    private String name;

    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Employee)) return false;
        Employee e = (Employee) o;
        return id == e.id && Objects.equals(name, e.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, name);
    }
}

```

Here:

- vTwo Employee objects with the same id and name are equal.
- They also produce the same hashCode(), keeping the contract valid.

### Describe Java Collections hierarchy.

[Java Collections Framework](../java/basics/collections/introduction#java-collections-interfaces)

1. What is the difference between a Set and a List?
2. What is the difference between a Set and a Map?
3. How do you sort an array?
4. How do you sort a List?

### Difference between Comparable and Comparator

Both **`Comparable`** and **`Comparator`** are interfaces in Java used for sorting objects.  
They belong to different packages and serve slightly different purposes:

- **`Comparable`** → Defines **natural ordering** inside the class itself.
- **`Comparator`** → Defines **custom or multiple sorting logics** outside the class.

| **Criteria**                    | **Comparable**                                    | **Comparator**                                                 |
| ------------------------------- | ------------------------------------------------- | -------------------------------------------------------------- |
| **Package**                     | `java.lang`                                       | `java.util`                                                    |
| **Method**                      | `int compareTo(Object o)`                         | `int compare(Object o1, Object o2)`                            |
| **Sorting Logic Location**      | Defined _inside_ the class being sorted.          | Defined _outside_ the class (separate comparator).             |
| **Used For**                    | Natural (default) sorting order.                  | Custom or multiple sorting orders.                             |
| **Affects Original Class**      | Modifies the class by implementing the interface. | Keeps the original class unchanged.                            |
| **Number of Sorting Sequences** | Only one sorting logic per class.                 | Multiple sorting logics can be implemented.                    |
| **Example Implementations**     | `String`, `Integer`, `Date` implement Comparable. | `Collections.sort(list, comparator)` uses Comparator.          |
| **Java 8+ Enhancements**        | N/A                                               | Can use Lambda expressions and `thenComparing()` for chaining. |

1. When would you use a Comparator instead of implementing Comparable? (to sort a Collection or an Array).
2. What is the difference between Vector and ArrayList?

| **Criteria**           | **ArrayList**                                           | **Vector**                                                                          |
| ---------------------- | ------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| **Package**            | `java.util`                                             | `java.util`                                                                         |
| **Synchronization**    | Not synchronized (⚡ faster).                           | Synchronized (🧵 thread-safe).                                                      |
| **Thread Safety**      | Not thread-safe (needs external synchronization).       | Thread-safe (methods are synchronized).                                             |
| **Performance**        | Faster, as no synchronization overhead.                 | Slower, due to synchronization on every method.                                     |
| **Growth Factor**      | Increases size by **50%** when full.                    | Increases size by **100%** (doubles capacity).                                      |
| **Legacy**             | Introduced in Java 1.2 (part of Collections Framework). | Legacy class from Java 1.0 (retained for backward compatibility).                   |
| **Iteration**          | Uses **Iterator** (fail-fast).                          | Uses **Enumeration** (not fail-fast) and **Iterator**.                              |
| **Fail-Fast Behavior** | Yes — throws `ConcurrentModificationException`.         | No — Enumeration is **not fail-fast**.                                              |
| **Preferred Usage**    | Recommended for modern, single-threaded applications.   | Rarely used — only when thread-safety is required without external synchronization. |

### What is the difference between Hashtable and HashMap?

Added in Map Page

### Describe how ClassLoader works.

In Java, **ClassLoader** is a part of the **Java Runtime Environment (JRE)** that dynamically loads classes into the **Java Virtual Machine (JVM)** during runtime.

When you run a Java program, classes are **not loaded all at once**.  
Instead, they are **loaded on demand** by the ClassLoader when the JVM needs them.

**How ClassLoader Works**

1. **When a class is needed**, the JVM asks the ClassLoader to load it.
2. The ClassLoader checks whether the class is **already loaded**.
3. If not, it follows the **delegation model**:
   - The **current ClassLoader** delegates the loading request to its **parent**.
   - This continues up to the **Bootstrap ClassLoader**.
   - If the parent cannot find the class, the **current loader** attempts to load it.
4. Once loaded, the class is stored in the JVM’s **method area** (a part of the memory).
5. The class is then used for execution.

**ClassLoader Hierarchy (Delegation Model)**

- Bootstrap ClassLoader
  (Loads **core Java classes** from the `rt.jar` or Java modules (`java.base`).)
  ↓
- Extension (Platform) ClassLoader
  (Loads classes from the **java extensions** (e.g., `lib/ext` directory or `java.ext.dirs` system property).)
  ↓
- Application (System) ClassLoader
  (Loads classes from the **classpath**)
  ↓
- Custom ClassLoader (if any)
  (You can create your own ClassLoader by extending `ClassLoader` and overriding the `findClass()` method.)

### What are the most important new APIs introduced by Java 8?

### What is a Stream?

A **Stream** in Java is a **sequence of data elements** that supports **functional-style operations** to process collections (like `List`, `Set`, `Map`, or arrays) efficiently.

**Key Characteristics of Streams**

| **Feature**            | **Description**                                                                           |
| ---------------------- | ----------------------------------------------------------------------------------------- |
| **Declarative**        | Focuses on _what_ to do rather than _how_ to do it.                                       |
| **Functional**         | Supports operations like `map`, `filter`, `reduce`.                                       |
| **Non-Storage**        | Streams don’t store data; they operate on existing collections.                           |
| **Lazy Evaluation**    | Intermediate operations are lazy — executed only when a terminal operation is invoked.    |
| **Parallel Execution** | Can easily run in parallel using `parallelStream()` for faster performance on large data. |

1. What is a Lambda expression?
1. What is a MethodHandle?
1. When would you use Optional? Provide an example.
1. How do you reference a constructor using a MethodHandle?
1. How does Java implements Generics? Why?
1. Did you use Reflection API? Provide an example.
1. Can you describe Producer Extends Consumer Super principle when designing an API involving generics?
1. How is HashMap implemented internally? How does it behave?
1. How is ConcurrentHashMap implemented internally? How does it behave?
1. What is ArrayList backed by?
1. How do ArrayList and LinkedList behave when adding an element in the middle of them?

## OOPs and Data Structures

1. What is the difference between a class and an object?
1. What is the difference between overloading and overriding a method?
1. What is the difference between composition and aggregation?
1. Can you override a static method?
1. Can you access an instance variable from a static method?
1. What is polymorphism in OOP?
1. What is the difference between procedural and OOP programming?
1. What is an abstract class and when would you use one?
1. What is an interface and when would you use one?
1. How do you implement a Stack?
1. How do you implement a Linked List?
1. What is the difference between a List and an Array?
1. What is the difference between a Trait and a Mixin?
1. How do you implement a Map?
1. Describe the pros and cons of Array List and Linked List?
1. What is a binary search tree?
1. What is a graph?
1. How do you implement a graph?
1. What is the difference between static and dynamic typing?
1. What is the difference between strong and weak typing?
1. Describe covariance and contravariance in OOP.

## Java Con-currency

1. What is the difference between process and thread?
1. Describe synchronize keyword. Provide an example.
1. Describe volatile keyword. Provide an example.
1. What is an object monitor?
1. What is a thread?
1. How do you create a thread in Java?
1. What is a ThreadPool?
1. What is a ThreadFactory and when would you use it?
1. Did you use java.util.concurrent package directly? Provide an example.
1. What is an Executor?
1. How do you stop a thread?
1. What is a daemon thread?
1. Describe a deadlock situation.
1. Describe a livelock situation.
1. What is a Future?
1. What is CompletableStage?
1. What is a CompletableFuture?
1. How do you schedule a task to execute every 5 minutes using only standard Java APIs?
1. What is a CyclicBarrier and when would you use it? Provide an example.
1. What is a CountDownLatch and when would you use it? Provide an example.
1. What is a ReentrantLock?
1. What are the advantages of using a Lock instead of synchronization?
1. Describe the Actor concurrency model.
1. Where can you use syncronized? What does the syncronize lock in each case?

## Java Tools and Frameworks:

1. What is your favourite Java framework?
1. Describe the pros and cons of your favourite Java framework.
1. What is ORM?
1. What ORM implementations did you use?
1. What JEE APIs did you use? Provide examples for each used.
1. What build tools did you use? Which one is your favourite and why?
1. What other JVM languages other than Java did you use?
1. What is DI and why it is useful?
1. What is AOP?
1. Spring specific questions.
1. JDBC, JPA, JTA, Servlets, JSP specific questions.
1. How would you build a microservice?

## JVM

1. Describe how the garbage collector works.
1. Describe JVM heap structure.
1. What garbage collector implementations are provided by JVM?
1. How can you increase JVM's heap size?
1. How can you create a memory leak in Java?
1. Describe Java Memory Model.
1. What is JIT?
1. Did you ever had to fine tune the JVM for optimal performance? Provide an example.
1. Describe how tiered compilation works in JVM.
1. What is the difference between '-client' and '-server' JVM parameters?

## Java Unit Testing:

1. What unit testing frameworks did you use?
1. Which unit testing framework is your favourite and why?
1. What is a unit test?
1. What is the difference between a unit test and an integration test?
1. What is TDD? Did you use it?
1. What is BDD? Did you use it?
1. What is the purpose of JUnit @Before and @After annotations?
1. What do you consider as hard-to-test-code? Provide an example.
1. What is mocking? What mocking libraries did you use?
1. What is the difference between mocks and stubs?
1. Difference between integration testing and unit testing? How would you implement each?

## Java Springboot

1. What is Spring Boot?
1. What are the advantages of Spring Boot?
1. What are the features of Spring Boot?
1. How to create Spring Boot application using Maven?
1. How to create Spring Boot project using Spring Initializer?
1. How to create Spring Boot project using boot CLI?
1. How to create simple Spring Boot application?
1. What are the Spring Boot Annotations?
1. What is Spring Boot dependency management?
1. What are the Spring Boot properties?
1. What are the Spring Boot Starters?
1. What is Spring Boot Actuator?
1. What is thymeleaf?
1. How to use thymeleaf?
1. How to connect Spring Boot to the database using JPA?
1. How to connect Spring Boot application to database using JDBC?
1. What is @RestController annotation in Spring Boot?
1. What is @RequestMapping annotation in Spring Boot?
1. How to create Spring Boot application using Spring Starter Project Wizard?
1. Spring Vs Spring Boot?

## Basic Design Concepts

1. Describe Singleton pattern and provide an implementation.
1. Describe your favourite design pattern and provide an usage example.
1. Describe SOLID design principles.
1. Describe your favourite design principle and provide an usage example.
1. Describe design pattern classification and provide an example for each category.
1. Describe Principle of Least Knowledge for OOP systems.
1. Describe the Diamond problem in the context of inheritance.
1. How to apply SOLID and OOPS principles together?
1. How UML helps in creating system design, explain different UML diagrams?
1. What is Test-driven development (TDD), explain?
1. What is Domain-driven design (DDD), explain?
1. What is high level design (HLD) and low level design (LLD), explain?
1. What is the benefits of SAGA pattern?
1. What is load balancing and how to achieve it?
1. What is Monolithic application, explain pros and cons?
1. How to redesign monolithic application?
1. Explain the difference between fault tolerance and fault resilience.

## Microservice

1. What is the difference between Monolith vs Microservices? When do you opt for Monolith vs Microservices?
1. What is the purpose of Docker?
1. Why is Docker recommended for Microservices?
1. What are the Microservices Architecture you have worked on?
1. What are cross cutting concerns in a microservice and how do you handle them?
1. How do you ensure resiliency in microservices? (retry mechanism using library like Resilience4j or Hystrix)
1. What is Repository pattern?
1. What is CQRS pattern?
1. What is API gateway pattern?
1. How do you secure/protect microservices? (OAuth implementation)
1. How do you implement communication between two services? (e.g. synchronous - REST call, asynchronous - message broker/service bus)
1. What is SQL vs NoSQL databases and when you opt for each?
1. How do you implement transactions in Microservices? (Distributed transaction using Saga pattern is one option)
1. What is Cohesion vs Coupling and how it translates to a better microservice?
1. What is circuit breaker mechanism and how do you implement?
1. What is Sidecar pattern? (used to implement cross cutting concerns)
1. How do you monitor and troubleshoot microservices?
1. What are the different web application hosting options provided by Azure with e.g.?
1. How do you troubleshoot slowness in microservices?
1. Is it better approach to have different database for different microservice or single database for all microservices?
1. Cons of microservice?
1. How to reduce or control throughput of microservice? What to keep in mind?
1. How to transfer a monolithic application to microservice?
1. What is bounded context?
1. Suppose you are moving a monolithic application to microservice in 1-year time frame. Now you have developed 2 modules in 3 months and let's assume 8 other modules are pending. In this scenario how will you maintain the application? (various practical examples)
1. Orchestration vs Choreography?
1. What are the REST principles you are following in your project?
1. Suppose you have developed a microservice API which has hit count 2000/s and every time this API uses a third-party API for data retrieval. Now your license for the third-party API permits hits 1000/s. How will you handle this scenario in terms of latency?

# GIT

1. What is a branch?
1. How can you create a branch?
1. What is the difference between git pull and git fetch?
1. How can current branch be changed?
1. How can content in a namespace/folder start being versioned using git?
1. Given your new project is git versioned, how do you get project code to your station?
1. How can you list latest commits on current branch?
1. How do you revert a commit that has already been pushed to remote repository?
1. How do you squash last N commits into a single commit?
1. Is git a centralized or distributed VCS? What does that mean?
1. What is reflog, why and how would you use it?
1. What does stash as a command do? When do you use it?
1. What are tags, when do you use them, and how do you publish them?
1. How do you push a branch a second time, after performing a rebase? Is it possible to not be able?
1. Explain the branching flow that you use in a complex project with many team members?
1. How can the revert on a merge commit be made?
1. What does git bisect do?
1. How can you check what parents a merge commit has?
1. What are git hooks, how can they be used, and for what?

## Security

1. What are the different things to consider regarding security of a web application?
1. What are the important things to consider regarding user authentication and authorization?
1. What are the important factors to consider when exposing an application to the Internet?
1. What are important security factors to consider in communicating with external interfaces?
1. Please let me know the list of authentication platforms used by you.
1. What are some essential features of Spring Security?
1. What do you mean by basic authentication?
1. What is OAuth and how does it work?
1. What OAuth-related libraries and frameworks have you used?
1. What is JWT and how is it structured?

## performance

1. What is the approach to analyse the response time of APIs and improve the performance?
1. How do we monitor the server for performance, memory usage and network traffic?
1. What is a concurrent user? How do we test with concurrent users to mimic the production usage?
1. ArrayList, LinkedList, and Vector are all implementations of the List interface. Which of them is most efficient for adding and removing elements from the list?
1. Why would it be more secure to store sensitive data (such as a password, social security number, etc.) in a character array rather than in a String?
1. Compare the sleep() and wait() methods in Java, including when and why you would use one vs. the other.

# Angular

## performance

1. How do you debug the page response time?
1. How do you handle and render in UI when 10,000 records are returned from an API call?
1. What is the approach to analyse the response time of APIs and improve the performance?
1. How to load large amounts of data in the UI?
1. As a web developer, how do you optimize your site’s loading time?
1. How can you reduce page loading time?
1. How to reduce the JS files loaded for a page?

## Security

1. How do you restrict input of vulnerable special characters in the screen?
1. How do you validate and avoid vulnerable special characters in the REST controller layer?
1. How do you encrypt the data when you call the APIs?
1. What is CORS? How do you handle CORS errors? [This can be asked with a scenario-based question instead of a direct one]
1. How do you sanitize your application's form input fields to prevent XSS attacks?
