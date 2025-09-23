Stream API in Java
==================

Introduction
------------

The **Stream API** was introduced in Java 8 as part of the ``java.util.stream`` package.  
It provides a modern, functional programming approach to processing collections of data.

Key Features:

* Supports **functional-style operations** on collections.
* Provides **declarative** programming (what to do, not how).
* Enables **parallel processing** for performance improvements.
* Works on collections, arrays, I/O channels, etc.

Stream vs Collection
--------------------

* **Collection**

  - Stores data (like ``List``, ``Set``, ``Map``).
  - Represents data structure.

* **Stream**

  - Represents a sequence of elements.
  - Does not store data, processes it.
  - Can be **consumed only once**.

Stream Pipeline
---------------

A stream operation consists of three parts:

1. **Source**: Collection, array, I/O channel, etc.
2. **Intermediate Operations**: Transform the stream (e.g., ``map()``, ``filter()``).
3. **Terminal Operation**: Produces a result (e.g., ``collect()``, ``forEach()``, ``reduce()``).

Example 1: Basic Stream Example
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java

    import java.util.Arrays;
    import java.util.List;

    public class StreamExample {
        public static void main(String[] args) {
            List<String> names = Arrays.asList("Revs", "Push", "Anjali", "Sanjana");

            names.stream()
                 .filter(name -> name.startsWith("S"))
                 .map(String::toUpperCase)
                 .forEach(System.out::println);
        }
    }

Output:

.. code-block::

    SANJANA

Explanation:

* ``stream()`` creates a stream from the list.
* ``filter()`` keeps only names starting with "S".
* ``map()`` transforms to uppercase.
* ``forEach()`` prints each element.

Example 2: Collecting Results
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java

    import java.util.Arrays;
    import java.util.List;
    import java.util.stream.Collectors;

    public class CollectExample {
        public static void main(String[] args) {
            List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

            List<Integer> evens = numbers.stream()
                                         .filter(n -> n % 2 == 0)
                                         .collect(Collectors.toList());

            System.out.println(evens);
        }
    }

Output:

.. code-block::

    [2, 4, 6]

Example 3: Parallel Streams
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java

    import java.util.stream.IntStream;

    public class ParallelStreamExample {
        public static void main(String[] args) {
            IntStream.range(1, 10)
                     .parallel()
                     .forEach(i -> {
                         System.out.println("Thread: " + Thread.currentThread().getName() + " Value: " + i);
                     });
        }
    }

Explanation:

* ``parallel()`` allows tasks to run in multiple threads.
* Useful for large datasets.

Example 4: Reduction
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java

    import java.util.Arrays;
    import java.util.List;

    public class ReduceExample {
        public static void main(String[] args) {
            List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

            int sum = numbers.stream()
                             .reduce(0, Integer::sum);

            System.out.println("Sum = " + sum);
        }
    }

Output:

.. code-block::

    Sum = 15

Common Stream Operations
------------------------

* **Intermediate Operations**
  
  - ``filter(Predicate)`` – filters elements.
  - ``map(Function)`` – transforms elements.
  - ``sorted()`` – sorts elements.
  - ``distinct()`` – removes duplicates.
  - ``limit(n)`` – takes first ``n`` elements.
  - ``skip(n)`` – skips first ``n`` elements.

* **Terminal Operations**
  
  - ``collect()`` – converts stream to collection.
  - ``forEach()`` – iterates elements.
  - ``reduce()`` – reduces elements to a single result.
  - ``count()`` – counts elements.
  - ``anyMatch()``, ``allMatch()``, ``noneMatch()``.

Use Cases
---------

* Filtering and transforming collections.
* Aggregating values (sum, average, min, max).
* Parallel data processing.
* Simplifying complex collection operations.

Summary
-------

* Stream API enables **functional programming** in Java.
* Works with **pipelines**: Source → Intermediate → Terminal.
* Supports **parallelism** for performance.
* Makes code **more readable and concise** compared to traditional loops.
