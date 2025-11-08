# 🌳 TreeSet

**TreeSet** is a **class in Java’s `java.util` package** that implements the **`NavigableSet`** interface (which extends `SortedSet`).

It stores **unique elements** (no duplicates) in a **sorted (ascending)** order.

---

## ⚙️ Class Hierarchy

```
java.lang.Object
   ↳ java.util.AbstractCollection
       ↳ java.util.AbstractSet
           ↳ java.util.TreeSet<E>
```

And it implements:

```java
Serializable, Cloneable, Iterable<E>, Collection<E>, Set<E>, SortedSet<E>, NavigableSet<E>
```

---

## 🧩 Key Features of TreeSet

| Feature                     | Description                                                 |
| --------------------------- | ----------------------------------------------------------- |
| **Duplicates**              | ❌ Not allowed                                              |
| **Order**                   | ✅ Sorted (natural order or custom `Comparator`)            |
| **Null elements**           | ❌ Not allowed (throws `NullPointerException` in Java 8+)   |
| **Thread-safe**             | ❌ No (but can use `Collections.synchronizedSortedSet()`)   |
| **Internal Data Structure** | ✅ **Red-Black Tree (a self-balancing binary search tree)** |
| **Performance**             | `add`, `remove`, `contains` – O(log n)                      |
| **Implements**              | `NavigableSet` for range queries, ceiling, floor, etc.      |

---

## 🧠 How TreeSet Works Internally

### 🌲 Data Structure — **Red-Black Tree**

Internally, `TreeSet` uses a **`TreeMap`** to store elements as **keys** (values are dummy).

```java
private transient NavigableMap<E, Object> m;
private static final Object PRESENT = new Object();
```

So when you do:

```java
TreeSet<Integer> set = new TreeSet<>();
set.add(10);
```

It actually calls:

```java
m.put(10, PRESENT);
```

Hence, TreeSet is just a **TreeMap-based wrapper** where:

- Keys = elements of the set
- Values = a dummy constant (`PRESENT`)

---

## 🧮 Ordering in TreeSet

- By default → **Natural ordering** (`Comparable`)
- You can define a **custom ordering** using a `Comparator`.

---

## 💻 Example 1: Natural Ordering

```java
import java.util.*;

public class TreeSetExample {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>();

        set.add(50);
        set.add(10);
        set.add(30);
        set.add(20);
        set.add(40);

        System.out.println("TreeSet: " + set);
    }
}
```

**🧩 Output:**

```
TreeSet: [10, 20, 30, 40, 50]
```

✅ Automatically sorted in ascending order (natural ordering of `Integer`).

---

## 💻 Example 2: Custom Comparator (Descending Order)

```java
import java.util.*;

public class TreeSetDescendingExample {
    public static void main(String[] args) {
        TreeSet<String> set = new TreeSet<>(Comparator.reverseOrder());

        set.add("Apple");
        set.add("Mango");
        set.add("Banana");

        System.out.println("TreeSet in Descending Order: " + set);
    }
}
```

**🧩 Output:**

```
TreeSet in Descending Order: [Mango, Banana, Apple]
```

---

## 💻 Example 3: Using TreeSet with Custom Objects

When you use your own class (like `Employee`), the objects must be **Comparable** or you must provide a **Comparator**.

```java
import java.util.*;

class Employee implements Comparable<Employee> {
    int id;
    String name;

    Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public int compareTo(Employee other) {
        return Integer.compare(this.id, other.id); // sort by id
    }

    @Override
    public String toString() {
        return "Employee{id=" + id + ", name='" + name + "'}";
    }
}

public class TreeSetObjectExample {
    public static void main(String[] args) {
        TreeSet<Employee> employees = new TreeSet<>();

        employees.add(new Employee(3, "John"));
        employees.add(new Employee(1, "Alice"));
        employees.add(new Employee(2, "Bob"));

        employees.forEach(System.out::println);
    }
}
```

**🧩 Output:**

```
Employee{id=1, name='Alice'}
Employee{id=2, name='Bob'}
Employee{id=3, name='John'}
```

✅ Automatically sorted by `id`.

---

## 💡 Important TreeSet Methods

| Method                   | Description                    |
| ------------------------ | ------------------------------ |
| `add(E e)`               | Adds element (O(log n))        |
| `remove(E e)`            | Removes element                |
| `contains(E e)`          | Checks if element exists       |
| `first()`                | Returns lowest element         |
| `last()`                 | Returns highest element        |
| `ceiling(E e)`           | Returns ≥ given element        |
| `floor(E e)`             | Returns ≤ given element        |
| `headSet(E toElement)`   | Returns elements < toElement   |
| `tailSet(E fromElement)` | Returns elements ≥ fromElement |
| `subSet(E from, E to)`   | Range view between elements    |
| `descendingSet()`        | Returns reverse order view     |

---

## 💻 Example 4: Using NavigableSet methods

```java
import java.util.*;

public class TreeSetNavigation {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>(Arrays.asList(10, 20, 30, 40, 50));

        System.out.println("Lower than 30: " + set.lower(30));     // 20
        System.out.println("Higher than 30: " + set.higher(30));   // 40
        System.out.println("Ceiling of 35: " + set.ceiling(35));   // 40
        System.out.println("Floor of 35: " + set.floor(35));       // 30
        System.out.println("Descending Set: " + set.descendingSet());
    }
}
```

**🧩 Output:**

```
Lower than 30: 20
Higher than 30: 40
Ceiling of 35: 40
Floor of 35: 30
Descending Set: [50, 40, 30, 20, 10]
```

---

## 🧠 Internal Implementation Summary

| Concept                       | Description                               |
| ----------------------------- | ----------------------------------------- |
| **Underlying Data Structure** | Red-Black Tree (via TreeMap)              |
| **Storage Mechanism**         | Key → element, Value → constant `PRESENT` |
| **Complexity**                | `add`, `remove`, `contains` → O(log n)    |
| **Sorting**                   | Natural or Comparator-based               |
| **Duplicates**                | Not allowed                               |
| **Null Elements**             | Not allowed (throws exception)            |

---

## 🧩 Comparison: TreeSet vs HashSet

| Feature        | HashSet      | TreeSet                  |
| -------------- | ------------ | ------------------------ |
| Ordering       | Unordered    | Sorted                   |
| Data Structure | HashMap      | TreeMap (Red-Black Tree) |
| Performance    | O(1) average | O(log n)                 |
| Nulls          | 1 allowed    | Not allowed              |
| Thread-safe    | ❌ No        | ❌ No                    |
| Custom sorting | ❌ No        | ✅ Yes (Comparator)      |

---

## ✅ Summary

> **TreeSet** in Java is a **sorted, unique, non-thread-safe** collection backed by a **Red-Black Tree** (via TreeMap).
> It provides **logarithmic performance** for insertion, deletion, and search and allows **custom sorting** through `Comparator`.
