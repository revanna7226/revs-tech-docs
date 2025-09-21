List Interface
==================

- A List is an ordered collection (also known as a sequence).
- built on Linear data structure.
- Lists can contain duplicate elements.
- List allows null elements.
- Elements in a List prserves the order of insertion and can be accessed by their integer index (position in the list).
- Lists can be resized dynamically as elements are added or removed.
- Lists can be iterated using loops or iterators.
- Lists can be converted to arrays and vice versa.
- Lists can be sorted and shuffled using utility methods from the Collections class.
- Lists can be synchronized for thread-safe operations using Collections.synchronizedList().
- Lists can be created using the List.of() method for immutable lists in Java 9 and later versions.

ArrayList
+++++++++++++++++++++++++++++++

.. topic:: Overview

   ``ArrayList`` is a resizable array implementation of the ``List`` interface 
   in the Java Collection Framework. Unlike arrays, the size of an 
   ``ArrayList`` grows dynamically as elements are added or removed.

.. topic:: Key Features

  - Implements the ``List`` interface.
  - Allows **duplicate elements**.
  - Maintains **insertion order**.
  - ArrayList class implements 3 marker interfaces: **Serializable**, **Cloneable**, and **RandomAccess**.
  - Allows **null elements**.
  - Provides **random access** (like arrays).
  - Not synchronized (use ``Collections.synchronizedList`` for thread-safety).
  - Default capacity is **10**, grows automatically when needed. NewCapacity = (OldCapacity * 3)/2 + 1.

.. topic:: Hierarchy

  - ``Iterable``
     - ``Collection``
        - ``List``
           - ``ArrayList``

.. topic:: Constructors

  - ``ArrayList()`` → Creates an empty list with default capacity (10).
  - ``ArrayList(int initialCapacity)`` → Creates an empty list with the specified initial capacity.
  - ``ArrayList(Collection c)`` → Creates a list containing the elements of the specified collection.

.. topic:: Code Examples

    .. code-block:: java

        import java.util.ArrayList;

        public class ArrayListExample {
            public static void main(String[] args) {
                // Create an ArrayList
                ArrayList<String> fruits = new ArrayList<>();

                // Add elements
                fruits.add("Apple");
                fruits.add("Banana");
                fruits.add("Mango");
                fruits.add("Banana"); // allows duplicates

                // Insert "Cherry" at index 2 (between Banana and Mango)
                fruits.add(2, "Cherry");

                // Access elements
                System.out.println("First fruit: " + fruits.get(0));

                // Iterate using for-each
                for (String fruit : fruits) {
                    System.out.println(fruit);
                }

                // Remove element
                fruits.remove("Mango");
                System.out.println("After removal: " + fruits);

                // Size of ArrayList
                System.out.println("Size: " + fruits.size());
            }
        }

.. topic:: Output

    .. code-block::

        First fruit: Apple
        Apple
        Banana
        Cherry
        Mango
        Banana
        After removal: [Apple, Banana, Cherry,  Banana]
        Size: 4

.. topic:: Advantages

  - Dynamic resizing.
  - Fast random access (``O(1)`` for ``get``).
  - Easy to use and widely supported.

.. topic:: Limitations

  - Insertion and deletion in the middle is slower (``O(n)``).
  - Not synchronized (must be handled explicitly in multithreaded environments).
  - If ArrayList grows beyond its capacity, it creates a new array and copies all elements, which can be costly in terms of performance.

.. topic:: When to Use

  - When you need a dynamic array that can grow and shrink.
  - When you need fast random access to elements.
  - When you need to maintain the order of elements and allow duplicates.
  - When you do not require frequent insertions and deletions in the middle of the list.
  - When you are working in a single-threaded environment or can manage synchronization manually.
  - When you need to frequently convert between arrays and lists.

Vector
+++++++++++++++++++++++++++++++

.. topic:: Overview

   ``Vector`` is a legacy class in the Java Collection Framework. It implements 
   a **growable array** of objects, similar to ``ArrayList``, but with one 
   major difference — ``Vector`` is **synchronized**, making it thread-safe 
   for multi-threaded environments.

.. topic:: Key Features

  - Implements the ``List`` interface.
  - Allows **duplicate elements**.
  - Maintains **insertion order**.
  - Provides **random access** like arrays.
  - **Synchronized** → safe for multithreaded use.
  - Slower than ``ArrayList`` in single-threaded applications due to synchronization overhead.
  - Default capacity is **10**, doubles automatically when capacity is exceeded.

.. topic:: Hierarchy

   - ``Iterable``
      - ``Collection``
         - ``List``
            - ``Vector``

.. topic:: Constructors

  - ``Vector()`` → Creates an empty vector with initial capacity 10.
  - ``Vector(int initialCapacity)`` → Creates an empty vector with specified capacity.
  - ``Vector(int initialCapacity, int capacityIncrement)`` → Creates a vector with specified capacity and increment step.
  - ``Vector(Collection c)`` → Creates a vector containing the elements of the specified collection.

.. topic:: Code Example

    .. code-block:: java

        import java.util.Vector;

        public class VectorExample {
            public static void main(String[] args) {
                // Create a Vector
                Vector<String> languages = new Vector<>();

                // Add elements
                languages.add("Java");
                languages.add("Python");
                languages.add("C++");
                languages.add("Java"); // allows duplicates

                // Access element
                System.out.println("First Language: " + languages.get(0));

                // Iterate using for-each
                for (String lang : languages) {
                    System.out.println(lang);
                }

                // Remove element
                languages.remove("C++");
                System.out.println("After removal: " + languages);

                // Check capacity and size
                System.out.println("Capacity: " + languages.capacity());
                System.out.println("Size: " + languages.size());
            }
        }

.. topic:: Output

    .. code-block::

        First Language: Java
        Java
        Python
        C++
        Java
        After removal: [Java, Python, Java]
        Capacity: 10
        Size: 3

.. topic:: Use Cases

  - **Multi-threaded applications** where thread-safety is required.
  - Legacy applications built before ``ArrayList`` (pre-Java 2).
  - When frequent random access is needed along with synchronization.
  - Suitable for small datasets in concurrent environments.

.. topic:: Advantages

  - Thread-safe due to synchronization.
  - Maintains insertion order.
  - Provides dynamic resizing.

.. topic:: Limitations

  - Slower compared to ``ArrayList`` because of synchronization overhead.
  - Considered a **legacy class**; in modern applications, prefer 
    ``ArrayList`` (single-threaded) or ``CopyOnWriteArrayList`` (thread-safe alternative).

LinkedList
+++++++++++++

.. topic:: Overview

   ``LinkedList`` is a part of the Java Collection Framework. It implements 
   the ``List`` and ``Deque`` interfaces, providing a **doubly-linked list** 
   data structure. Unlike ``ArrayList`` (which uses a dynamic array), 
   ``LinkedList`` uses nodes, where each node stores data and references to 
   the next and previous nodes.

.. topic:: Key Features

  - Implements ``List`` and ``Deque`` interfaces.
  - Allows **duplicate elements**.
  - Maintains **insertion order**.
  - Provides efficient insertion and deletion operations (``O(1)`` for adding/removing at ends).
  - Slower random access compared to ``ArrayList`` (``O(n)`` for ``get``).
  - Can be used as a **List**, **Queue**, or **Deque**.

.. topic:: Hierarchy

   - ``Iterable``
      - ``Collection``
         - ``List``
            - ``LinkedList``
         - ``Queue``
            - ``Deque``
               - ``LinkedList``

.. topic:: Constructors

  - ``LinkedList()`` → Creates an empty linked list.
  - ``LinkedList(Collection c)`` → Creates a linked list containing the elements of the specified collection.

.. topic:: Code Example

    .. code-block:: java

        import java.util.LinkedList;

        public class LinkedListExample {
            public static void main(String[] args) {
                // Create a LinkedList
                LinkedList<String> animals = new LinkedList<>();

                // Add elements
                animals.add("Dog");
                animals.add("Cat");
                animals.add("Horse");
                animals.add("Dog"); // allows duplicates

                // Add elements at first and last
                animals.addFirst("Elephant");
                animals.addLast("Tiger");

                // Access elements
                System.out.println("First animal: " + animals.getFirst());
                System.out.println("Last animal: " + animals.getLast());

                // Iterate using for-each
                for (String animal : animals) {
                    System.out.println(animal);
                }

                // Remove elements
                animals.remove("Cat");
                animals.removeFirst();
                animals.removeLast();
                System.out.println("After removals: " + animals);

                // Size of LinkedList
                System.out.println("Size: " + animals.size());
            }
        }

.. topic:: Output

    .. code-block::

        First animal: Elephant
        Last animal: Tiger
        Elephant
        Dog
        Cat
        Horse
        Dog
        Tiger
        After removals: [Dog, Horse, Dog]
        Size: 3

.. topic:: Use Cases

  - When frequent insertions and deletions are required (especially in the middle of the list).
  - Useful for **queue** and **deque** implementations.
  - Suitable for applications where traversal is mostly sequential rather than random access.
  - Can be used to implement **stacks, queues, and double-ended queues**.

.. topic:: Advantages

  - Faster insertions and deletions compared to ``ArrayList``.
  - Can function as both ``List`` and ``Deque``.
  - No memory wastage like array resizing.

.. topic:: Limitations

  - Slower random access compared to ``ArrayList`` (must traverse nodes).
  - Higher memory overhead (extra references for previous and next nodes).
  - Not synchronized (must use ``Collections.synchronizedList`` or 
    concurrent alternatives for thread safety).

ArrayList vs Vector vs LinkedList
+++++++++++++++++++++++++++++++++++++++++++

.. list-table:: Difference between ArrayList, Vector and LinkedList
   :header-rows: 1
   :widths: 10 30 30 30

   * - Feature
     - ArrayList
     - Vector
     - LinkedList
   * - Definition
     - A resizable array implementation of ``List``.
     - A legacy synchronized implementation of ``List``.
     - A doubly-linked list implementation of ``List`` and ``Deque``.
   * - Thread Safety
     - Not synchronized (must use ``Collections.synchronizedList``).
     - Synchronized (thread-safe).
     - Not synchronized.
   * - Performance
     - Faster in single-threaded environments due to no synchronization overhead.
     - Slower than ``ArrayList`` because every method is synchronized.
     - Efficient for frequent insertions and deletions, slower for random access.
   * - Data Access
     - Provides fast random access (``O(1)``).
     - Provides fast random access (``O(1)``).
     - Random access is slower (``O(n)``).
   * - Insertion/Deletion
     - Slower (shifting required if in middle).
     - Slower (synchronized + shifting required).
     - Faster (only node links are updated).
   * - Memory Usage
     - Less memory overhead (just array storage).
     - Similar to ``ArrayList`` but with synchronization overhead.
     - Higher memory usage (extra node references).
   * - Null Handling
     - Allows multiple ``null`` values.
     - Allows multiple ``null`` values.
     - Allows multiple ``null`` values.
   * - Use Cases
     - Best for fast lookups and iteration in non-threaded apps.
     - Suitable for multi-threaded apps needing synchronized list.
     - Best for apps with frequent insertions/deletions (queues, deques).
   * - Legacy/Modern
     - Modern class (preferred over ``Vector``).
     - Legacy class (introduced in Java 1.0).
     - Modern class, versatile as ``List`` and ``Deque``.
