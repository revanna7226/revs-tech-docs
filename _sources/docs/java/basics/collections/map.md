# Map Interface

- HashMap
- LinkedHashMap
- TreeMap
- ConcurrentHashmap

## Difference Between Hashtable and HashMap

| **Criteria**                      | **HashMap**                                                                | **Hashtable**                                                     |
| --------------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------- |
| **Package**                       | `java.util`                                                                | `java.util`                                                       |
| **Synchronization**               | Not synchronized (⚡ faster, not thread-safe).                             | Synchronized (🧵 thread-safe).                                    |
| **Thread Safety**                 | Not thread-safe (requires external synchronization).                       | Thread-safe (all methods are synchronized).                       |
| **Performance**                   | Faster (no synchronization overhead).                                      | Slower (due to synchronization).                                  |
| **Null Keys/Values**              | Allows one `null` key and multiple `null` values.                          | ❌ Does **not** allow `null` keys or values.                      |
| **Legacy Status**                 | Introduced in Java 1.2 (modern).                                           | Legacy class from Java 1.0 (retained for backward compatibility). |
| **Iterator Type**                 | Uses **Iterator** (fail-fast).                                             | Uses **Enumeration** (not fail-fast).                             |
| **Fail-Fast Behavior**            | Yes — throws `ConcurrentModificationException`.                            | No — Enumeration is not fail-fast.                                |
| **Order**                         | Does not maintain insertion order.                                         | Does not maintain insertion order.                                |
| **Alternative for Thread Safety** | Use `Collections.synchronizedMap(new HashMap<>())` or `ConcurrentHashMap`. | Thread-safe by default, but outdated.                             |
| **Preferred Usage**               | Modern, non-threaded applications.                                         | Legacy or when old codebase requires it.                          |

## Difference Between Set and Map in Java

| **Criteria**               | **Set**                                                               | **Map**                                                                   |
| -------------------------- | --------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **Definition**             | A collection that contains **only unique elements**.                  | A collection of **key-value pairs** where each key is unique.             |
| **Package**                | `java.util.Set`                                                       | `java.util.Map`                                                           |
| **Storage Structure**      | Stores only **values (elements)**.                                    | Stores **key-value pairs (entries)**.                                     |
| **Uniqueness**             | No duplicate elements are allowed.                                    | Duplicate **keys** are not allowed, but **values** can be duplicated.     |
| **Access Mechanism**       | Accessed directly by element (value).                                 | Accessed using a **key** to get the corresponding **value**.              |
| **Null Handling**          | Can contain at most **one null element** (depends on implementation). | Can contain **one null key** and **multiple null values** (in `HashMap`). |
| **Common Implementations** | `HashSet`, `LinkedHashSet`, `TreeSet`.                                | `HashMap`, `LinkedHashMap`, `TreeMap`, `Hashtable`.                       |
| **Ordering**               | Unordered (unless using `LinkedHashSet` or `TreeSet`).                | Unordered (unless using `LinkedHashMap` or `TreeMap`).                    |
| **Usage**                  | When you only need to store **unique elements**.                      | When you need to store **key-value associations** for quick lookups.      |
| **Example**                | `Set<String> names = new HashSet<>();`                                | `Map<Integer, String> studentMap = new HashMap<>();`                      |
