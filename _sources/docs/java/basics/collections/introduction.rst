Collections
=================

A collection in Java is a group of individual objects that are treated as a single unit. In Java, a separate framework named the "Collection Framework" was defined in JDK 1.2, which contains all the Java Collection Classes and interfaces. 

In Java, the Collection interface (java.util.Collection) and Map interface (java.util.Map) are the two main “root” interfaces of Java collection classes.

**Fundamentals of Java Collection Framework**

- It can resize on it's own when elements are added or removed.
- Any instance/object added to collection is casted to Object class and stored in the collection.
- When an instance/object is retrieved from the collection, it needs to be cast back to its original type.
- It can hold heterogeneous objects (objects of different types).

**Needed for a Collection Framework**

Before the Collection Framework (before JDK 1.2), Java used Arrays, Vectors, and Hashtables to group objects, but they lacked a common interface. Each had a separate implementation, making usage inconsistent and harder for developers to learn and maintain.

**Advantages of the Java Collection Framework**

Since the lack of a collection framework gave rise many disadvantages, the following are the advantages of the collection framework. 

#. Consistent API: Interfaces like List, Set, and Map have common methods across classes (ArrayList, LinkedList, etc.).
#. Less Coding Effort: Developers focus on usage, not designing data structures—supports OOP abstraction.
#. Better Performance: Offers fast, reliable implementations of data structures, improving speed and quality of code.

Java Collections Interfaces
----------------------------------------------------------------

The collection framework contains multiple interfaces where every interface is used to store a specific type of data. The following are the interfaces present in the framework. 

Collection Interfaces and Classes
+++++++++++++++++++++++++++++++++++++++++++++++

The Collection interface extends Iterable and serves as the root, defining common methods inherited by all collection classes. The collection framework contains multiple interfaces where every interface is used to store a specific type of data.

- Iterable (root interface)
  
  - Collection (extends Iterable)
    
    - List (ordered, allows duplicates)
      
      - ArrayList
      - LinkedList
      - Vector

        - Stack

    - Set (no duplicates, unordered/ordered depending on implementation)
      
      - HashSet
       
        - LinkedHashSet
      - TreeSet

    - Queue (FIFO order, but variations possible)
      
      - PriorityQueue
      - ArrayDeque
      - LinkedList (also implements List)

1. **Iterable Interface**
   
   Iterable interface is the root of the Collection Framework. It is extended by the Collection interface, making all collections inherently iterable. Its primary purpose is to provide an Iterator to traverse elements, defined by its single abstract method iterator().

   .. code-block:: java

        public interface Iterable<T> {
            Iterator<T> iterator();
        }

2. **Collection Interface**
   
   Collection interface extends Iterable and serves as the foundation of the Collection Framework. 
   It defines common methods like add(), remove(), and clear(), ensuring consistency and reusability across all collection implementations.


3. **List Interface**
   
   List interface extends the Collection interface and represents an ordered collection that allows duplicate elements. 
   It is implemented by classes like ArrayList, Vector, and Stack.

   .. code-block:: java

        List <T> al = new ArrayList<> (); 
        List <T> ll = new LinkedList<> (); 
        List <T> v = new Vector<> (); 
        // Where T is the type of the object    

4. **Queue Interface**
   
   The Queue interface follows the FIFO (First-In, First-Out) principle, where elements are processed in the order they are added—similar to a real-world queue (e.g., ticket booking). It is used when order matters. Classes like PriorityQueue and ArrayDeque implement this interface, allowing queue objects to be instantiated accordingly.

   For example:
   
   .. code-block:: java

        Queue <T> pq = new PriorityQueue<> (); 
        Queue <T> ad = new ArrayDeque<> (); 
        Where T is the type of the object.  

   The most frequently used implementation of the queue interface is the PriorityQueue.         

5. **Deque Interface**
   Deque interface extends Queue and allows insertion and removal of elements from both ends. It is implemented by classes like ArrayDeque, which can be used to instantiate a Deque object.

   For example:

   .. code-block:: java

        Deque<T> ad = new ArrayDeque<> (); 
        Where T is the type of the object.  

   The class which implements the deque interface is ArrayDeque.


6. **Set Interface**

   Set interface represents an unordered collection that stores only unique elements (no duplicates). It's implemented by classes like HashSet, TreeSet, and LinkedHashSet, and can be instantiated using any of these
   For example:

   .. code-block:: java

        Set<T> hs = new HashSet<> (); 
        Set<T> lhs = new LinkedHashSet<> (); 
        Set<T> ts = new TreeSet<> (); 
        Where T is the type of the object.     

7. **Sorted Set Interface**
   
   Sorted Set interface extends Set and maintains elements in sorted order. It includes additional methods for range views and ordering. It is implemented by the TreeSet class.

   For example:

   .. code-block:: java

        SortedSet<T> ts = new TreeSet<> (); 
        Where T is the type of the object. 

   The class which implements the sorted set interface is TreeSet.         


Map Interface
----------------

Map is a data structure that supports the key-value pair for mapping the data. 
This interface doesn't support duplicate keys because the same key cannot have multiple mappings, 
however, it allows duplicate values in different keys. 
A map is useful if there is data and we wish to perform operations on the basis of the key. 
This map interface is implemented by various classes like HashMap, TreeMap, etc. 
Since all the subclasses implement the map, we can instantiate a map object with any of these classes.    

Map Interfaces and Classes
+++++++++++++++++++++++++++++++++++++++++++++++

- Map (key–value pairs, no duplicate keys)
  
  - HashMap
    - LinkedHashMap
  - Hashtable
  - TreeMap
  - WeakHashMap
  - IdentityHashMap
  - ConcurrentHashMap
- SortedMap (sub-interface of Map)
  - TreeMap

Collections vs Map in Java
==========================

.. list-table:: Difference between Collection and Map
   :header-rows: 1
   :widths: 10 45 45

   * - Feature
     - Collection
     - Map
   * - Definition
     - A group of individual objects treated as a single unit.
     - An object that maps keys to values (key–value pairs).
   * - Interface
     - Root interface is ``Collection`` (extends ``Iterable``).
     - Root interface is ``Map`` (separate from ``Collection``).
   * - Elements
     - Stores only elements (objects).
     - Stores entries (key–value pairs).
   * - Uniqueness
     - Allows duplicate elements depending on implementation (e.g., ``List`` allows duplicates, ``Set`` does not).
     - Keys must be unique, but values can be duplicated.
   * - Access
     - Access elements directly (via index in ``List`` or iteration in ``Set``/``Queue``).
     - Access values using keys.
   * - Examples
     - ``List`` (ArrayList, LinkedList), ``Set`` (HashSet, TreeSet), ``Queue`` (PriorityQueue).
     - ``HashMap``, ``TreeMap``, ``LinkedHashMap``, ``Hashtable``.
   * - Null Handling
     - Many implementations allow multiple ``null`` values (e.g., ``List``).
     - At most one ``null`` key allowed in ``HashMap``; multiple ``null`` values allowed.
   * - Usage
     - Suitable when only values are needed without unique keys.
     - Suitable when data must be stored and retrieved using unique keys.
