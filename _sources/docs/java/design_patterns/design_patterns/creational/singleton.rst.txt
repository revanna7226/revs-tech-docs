Singleton Design Pattern in Java
===============================

#. Overview: 
    The **Singleton Design Pattern** is a **creational pattern** that ensures a class has only **one instance** and provides a **global point of access** to it.  
    It is widely used in **logging, configuration, caching, and thread pools**.


#. Types of Singleton Implementation:
    .. list-table::
        :header-rows: 1
        :widths: 25 75

        * - **Type**
          - **Description**
        * - Eager Initialization
          - Instance is created at the time of class loading. Simple but may waste resources.
        * - Lazy Initialization
          - Instance is created only when requested. Not thread-safe without synchronization.
        * - Thread-safe Singleton
          - Ensures only one instance is created in multi-threaded environments using `synchronized`.
        * - Bill Pugh Singleton
          - Uses a static inner helper class for lazy-loaded, thread-safe singleton.
        * - Enum Singleton
          - Uses Java Enum, inherently thread-safe, handles serialization automatically.

    #. Eager Initialization:
        The instance is created at the time of class loading.

        .. code-block:: java

            public class EagerSingleton {
                private static final EagerSingleton instance = new EagerSingleton();

                private EagerSingleton() {
                    // private constructor
                }

                public static EagerSingleton getInstance() {
                    return instance;
                }
            }

        **Pros:**
            - Simple implementation.
            - Thread-safe without synchronization.

        **Cons:**
            - Instance is created even if not used (resource wastage).

    #. Lazy Initialization:
        Instance is created only when needed.

        .. code-block:: java

            public class LazySingleton {
                private static LazySingleton instance;

                private LazySingleton() {}

                public static LazySingleton getInstance() {
                    if (instance == null) {
                        instance = new LazySingleton();
                    }
                    return instance;
                }
            }

        **Pros:**
        - Lazy loading (created only when needed).

        **Cons:**
        - Not thread-safe.
        - Multiple threads may create multiple instances.

    #. Thread-Safe Singleton:
        Using `synchronized` to make lazy initialization thread-safe.

        .. code-block:: java

            public class ThreadSafeSingleton {
                private static ThreadSafeSingleton instance;

                private ThreadSafeSingleton() {}

                public static synchronized ThreadSafeSingleton getInstance() {
                    if (instance == null) {
                        instance = new ThreadSafeSingleton();
                    }
                    return instance;
                }
            }

        **Pros:**
        - Thread-safe.

        **Cons:**
        - Synchronization overhead on every call.

    #. Double-Checked Locking:
        Reduces synchronization overhead by locking only when instance is null.

        .. code-block:: java

            public class DoubleCheckedSingleton {
                private static volatile DoubleCheckedSingleton instance;

                private DoubleCheckedSingleton() {}

                public static DoubleCheckedSingleton getInstance() {
                    if (instance == null) {
                        synchronized (DoubleCheckedSingleton.class) {
                            if (instance == null) {
                                instance = new DoubleCheckedSingleton();
                            }
                        }
                    }
                    return instance;
                }
            }

        **Pros:**
        - Thread-safe.
        - Efficient (synchronization only at first time).

        **Cons:**
        - Requires `volatile` keyword (Java 5+).


    #. Bill Pugh Singleton Implementation:
        Uses a static inner helper class. Lazy-loaded and thread-safe.

        .. code-block:: java

            public class BillPughSingleton {
                private BillPughSingleton() {}

                private static class SingletonHelper {
                    private static final BillPughSingleton INSTANCE = new BillPughSingleton();
                }

                public static BillPughSingleton getInstance() {
                    return SingletonHelper.INSTANCE;
                }
            }

        **Pros:**
        - Thread-safe without synchronization.
        - Lazy-loaded.

    #. Enum Singleton:
        Java Enum approach is **simplest and safest**, handles serialization automatically.

        .. code-block:: java

            public enum EnumSingleton {
                INSTANCE;

                public void showMessage() {
                    System.out.println("Hello from Enum Singleton!");
                }
            }

        **Usage:**

        .. code-block:: java

            public class Main {
                public static void main(String[] args) {
                    EnumSingleton singleton = EnumSingleton.INSTANCE;
                    singleton.showMessage();
                }
            }

        **Pros:**
        - Thread-safe.
        - Serialization handled automatically.
        - Reflection attack resistant.

#. Comparison of Singleton Implementations:
    .. list-table::
        :header-rows: 1
        :widths: 20 20 20 40

        * - **Type**
          - **Thread-Safe**
          - **Lazy Loading**
          - **Pros/Cons**
        * - Eager Initialization
          - Yes
          - No
          - Simple, but may waste resources.
        * - Lazy Initialization
          - No
          - Yes
          - Lazy loading, but not thread-safe.
        * - Thread-Safe Singleton (synchronized)
          - Yes
          - Yes
          - Thread-safe, but synchronization overhead.
        * - Double-Checked Locking
          - Yes
          - Yes
          - Efficient, thread-safe, requires volatile.
        * - Bill Pugh Singleton
          - Yes
          - Yes
          - Efficient, lazy-loaded, elegant.
        * - Enum Singleton
          - Yes
          - Yes
          - Simplest, handles serialization and reflection attacks.

#. Usage in Spring Framework:
    - Spring Beans are **singleton by default**, using the Singleton pattern.
  
    Example:

    .. code-block:: java

        @Service
        public class UserService {
            public void processUser() {
                System.out.println("Processing user...");
            }
        }

    Spring ensures only **one instance** of `UserService` exists in the application context.
        
