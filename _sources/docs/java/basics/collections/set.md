# Set Interface

- A Set is a collection that cannot contain duplicate elements.
- built on Hashing data structure.
- Sets are unordered collections, meaning they do not maintain the order of insertion.
- Set does not allow null elements (except for HashSet which allows one null element).
- Elements in a Set cannot be accessed by an index since they do not preserve the order of insertion.
- Sets can be iterated using loops or iterators.
- Sets can be converted to arrays and vice versa.
- Sets can be synchronized for thread-safe operations using Collections.synchronizedSet().
- Sets can be created using the Set.of() method for immutable sets in Java 9 and later versions.
- Sets can be implemented using different classes like HashSet, LinkedHashSet, and TreeSet, each with its own characteristics regarding ordering and performance.
- Sets can be used in various applications like removing duplicates from a collection, membership testing, and implementing mathematical sets.

## Difference Between HashSet, LinkedHashSet and TreeSet

| **Criteria**                      | **HashSet**                                                    | **LinkedHashSet**                                    | **TreeSet**                                                 |
| --------------------------------- | -------------------------------------------------------------- | ---------------------------------------------------- | ----------------------------------------------------------- |
| **Ordering**                      | No guaranteed order of elements.                               | Maintains insertion order.                           | Maintains elements in **sorted (natural or custom)** order. |
| **Underlying Data Structure**     | Uses a **HashMap** internally.                                 | Uses a **LinkedHashMap** internally.                 | Uses a **TreeMap** (Red-Black tree) internally.             |
| **Performance (Time Complexity)** | O(1) for add, remove, contains (on average).                   | O(1) for add, remove, contains (on average).         | O(log n) for add, remove, contains.                         |
| **Null Elements**                 | Allows one `null` element.                                     | Allows one `null` element.                           | Does **not** allow `null` (throws `NullPointerException`).  |
| **Order Preservation**            | ❌ Not preserved.                                              | ✅ Preserves insertion order.                        | ✅ Preserves sorted order.                                  |
| **Sorting**                       | ❌ Not sorted.                                                 | ❌ Not sorted.                                       | ✅ Sorted automatically.                                    |
| **Use Case**                      | When you need a fast, unordered collection of unique elements. | When you need insertion order and uniqueness.        | When you need elements sorted in natural or custom order.   |
| **Example**                       | `HashSet<String> set = new HashSet<>();`                       | `LinkedHashSet<String> set = new LinkedHashSet<>();` | `TreeSet<String> set = new TreeSet<>();`                    |

## Difference between List and Set

| **Criteria**               | **List**                                                 | **Set**                                                                                          |
| -------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **Definition**             | An ordered collection that allows duplicate elements.    | An unordered collection that does not allow duplicate elements.                                  |
| **Order of Elements**      | Maintains insertion order.                               | Does not guarantee order (except in specific implementations like `LinkedHashSet` or `TreeSet`). |
| **Duplicates**             | Allows duplicate values.                                 | Does not allow duplicates.                                                                       |
| **Indexing**               | Elements can be accessed by index (e.g., `list.get(0)`). | No index-based access.                                                                           |
| **Null Values**            | Can contain multiple `null` values.                      | Can contain at most one `null` value (depends on implementation).                                |
| **Common Implementations** | `ArrayList`, `LinkedList`, `Vector`, `Stack`.            | `HashSet`, `LinkedHashSet`, `TreeSet`.                                                           |
| **Performance**            | Faster for positional access and iteration.              | Faster for search, insertion, and deletion (especially `HashSet`).                               |
| **Use Case**               | When order and duplicates matter.                        | When uniqueness of elements matters.                                                             |
