# 🌳 What is `TreeMap` in Java?

`TreeMap` is a class in **`java.util`** package that implements the **`NavigableMap`** interface, which extends `SortedMap` and `Map`.
It stores **key–value pairs** in **sorted order of keys**.

👉 It’s part of the Java Collections Framework and was introduced in **Java 1.2**.

---

## ⚙️ Key Characteristics

| Feature                       | Description                                                                                                                                                |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Ordering**                  | Keys are always **sorted** either by their **natural ordering** (if they implement `Comparable`) or by a **custom `Comparator`** provided at map creation. |
| **Underlying Data Structure** | **Self-balancing Red-Black Tree**                                                                                                                          |
| **Time Complexity**           | O(log n) for `get()`, `put()`, `remove()`                                                                                                                  |
| **Null Keys/Values**          | ❌ Does **not allow null keys**, ✅ allows **multiple null values**                                                                                        |
| **Thread Safety**             | ❌ Not synchronized (use `Collections.synchronizedMap()` if needed)                                                                                        |
| **Duplicates**                | ❌ No duplicate keys                                                                                                                                       |

---

## 🧱 Data Structure Used

### 🔺 Red-Black Tree

Internally, `TreeMap` is implemented using a **Red-Black Tree**, which is a type of **self-balancing Binary Search Tree (BST)**.

### 🧩 Red-Black Tree Properties

1. Every node is **either red or black**.
2. The **root node** is always **black**.
3. No two **consecutive red nodes** (parent & child) are allowed.
4. Every path from the root to a leaf has the **same number of black nodes**.
5. The tree **balances itself** during insertion/deletion to maintain these properties.

Because of these rules, operations like **search**, **insert**, and **delete** all happen in **O(log n)** time.

---

## 🧠 Internal Structure

Internally, the `TreeMap` stores entries as **objects of an inner class** like this (simplified):

```java
static final class Entry<K,V> implements Map.Entry<K,V> {
    K key;
    V value;
    Entry<K,V> left;
    Entry<K,V> right;
    Entry<K,V> parent;
    boolean color = BLACK; // red or black node

    Entry(K key, V value, Entry<K,V> parent) {
        this.key = key;
        this.value = value;
        this.parent = parent;
    }
}
```

When you call `put(key, value)`:

- The key is compared using `compareTo()` or `Comparator.compare()`.
- It’s inserted in sorted order.
- If tree properties are violated, **rebalancing** is done automatically.

---

# 🧩 Basic Example — Natural Ordering

```java
import java.util.TreeMap;

public class TreeMapExample {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();

        map.put(3, "Banana");
        map.put(1, "Apple");
        map.put(2, "Orange");
        map.put(4, "Mango");

        System.out.println("TreeMap: " + map);
    }
}
```

### 🟢 Output

```
TreeMap: {1=Apple, 2=Orange, 3=Banana, 4=Mango}
```

> Keys are automatically sorted in **ascending order** (`1, 2, 3, 4`).

---

# 🧩 Example — Custom Sorting using Comparator

```java
import java.util.*;

public class TreeMapCustomComparator {
    public static void main(String[] args) {
        Comparator<String> reverseOrder = (a, b) -> b.compareTo(a);
        TreeMap<String, Integer> map = new TreeMap<>(reverseOrder);

        map.put("Banana", 3);
        map.put("Apple", 1);
        map.put("Mango", 4);

        System.out.println("Descending order: " + map);
    }
}
```

### 🟢 Output

```
Descending order: {Mango=4, Banana=3, Apple=1}
```

---

# 🧩 Example — NavigableMap Features

`TreeMap` implements `NavigableMap`, which adds methods for **range queries** and **navigation**.

```java
import java.util.*;

public class TreeMapNavigation {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();
        map.put(10, "Ten");
        map.put(20, "Twenty");
        map.put(30, "Thirty");
        map.put(40, "Forty");

        System.out.println("Lower Key (<30): " + map.lowerKey(30));   // 20
        System.out.println("Higher Key (>30): " + map.higherKey(30)); // 40
        System.out.println("Floor Key (<=30): " + map.floorKey(30));  // 30
        System.out.println("Ceiling Key (>=25): " + map.ceilingKey(25)); // 30
        System.out.println("First Entry: " + map.firstEntry());
        System.out.println("Last Entry: " + map.lastEntry());
    }
}
```

---

# 🧩 Example — Submap, Headmap, Tailmap

```java
import java.util.TreeMap;

public class TreeMapRanges {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();
        map.put(1, "A");
        map.put(3, "C");
        map.put(5, "E");
        map.put(7, "G");

        System.out.println("Original: " + map);
        System.out.println("HeadMap (<5): " + map.headMap(5));   // keys < 5
        System.out.println("TailMap (>=5): " + map.tailMap(5)); // keys ≥ 5
        System.out.println("SubMap (3–7): " + map.subMap(3, 7)); // [3,7)
    }
}
```

---

# ⚙️ How `put()` Works Internally (Simplified)

Here’s a conceptual flow of how the `put()` method works inside TreeMap:

```java
public V put(K key, V value) {
    if (root == null) {
        root = new Entry<>(key, value, null);
        size = 1;
        return null;
    }

    Entry<K,V> parent;
    Entry<K,V> current = root;
    int cmp;
    do {
        parent = current;
        cmp = compare(key, current.key);
        if (cmp < 0) current = current.left;
        else if (cmp > 0) current = current.right;
        else return current.setValue(value); // duplicate key → overwrite
    } while (current != null);

    Entry<K,V> e = new Entry<>(key, value, parent);
    if (cmp < 0) parent.left = e;
    else parent.right = e;

    fixAfterInsertion(e); // 🔴 Rebalance Red-Black Tree
    size++;
    return null;
}
```

---

# 📊 Time Complexity

| Operation   | Average  | Worst Case | Explanation            |
| ----------- | -------- | ---------- | ---------------------- |
| `get()`     | O(log n) | O(log n)   | Tree traversal         |
| `put()`     | O(log n) | O(log n)   | Insert + rebalance     |
| `remove()`  | O(log n) | O(log n)   | Delete + rebalance     |
| `iteration` | O(n)     | O(n)       | Sorted order traversal |

---

# 🚫 `TreeMap` vs Other Maps

| Feature                       | `HashMap`   | `LinkedHashMap`          | `TreeMap`             |
| ----------------------------- | ----------- | ------------------------ | --------------------- |
| **Order**                     | Unordered   | Insertion order          | Sorted by key         |
| **Null Key**                  | 1 allowed   | 1 allowed                | ❌ Not allowed        |
| **Time Complexity (get/put)** | O(1)        | O(1)                     | O(log n)              |
| **Data Structure**            | Hash table  | Hash table + linked list | Red-Black Tree        |
| **Thread Safety**             | ❌          | ❌                       | ❌                    |
| **Use Case**                  | Fast lookup | Maintain insertion order | Maintain sorted order |

---

# 🧩 Example — TreeMap with Custom Object Keys

When using custom classes as keys, they **must implement `Comparable`** or be used with a custom `Comparator`.

```java
import java.util.*;

class Student implements Comparable<Student> {
    int id;
    String name;

    Student(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.id, other.id);
    }

    public String toString() {
        return name + "(" + id + ")";
    }
}

public class TreeMapCustomKey {
    public static void main(String[] args) {
        TreeMap<Student, String> map = new TreeMap<>();

        map.put(new Student(3, "John"), "A");
        map.put(new Student(1, "Alice"), "B");
        map.put(new Student(2, "Bob"), "C");

        System.out.println(map);
    }
}
```

### 🟢 Output

```
{Alice(1)=B, Bob(2)=C, John(3)=A}
```

---

# 🧷 Summary

| Feature            | Description                                                              |
| ------------------ | ------------------------------------------------------------------------ |
| **Implements**     | `NavigableMap<K,V>`                                                      |
| **Data Structure** | Red-Black Tree                                                           |
| **Ordering**       | Sorted by keys                                                           |
| **Complexity**     | O(log n) for most ops                                                    |
| **Nulls**          | No null keys, allows null values                                         |
| **Thread Safety**  | Not synchronized                                                         |
| **Use Case**       | When you need sorted maps (e.g. leaderboard, ranges, floor/ceiling keys) |

---

✅ **In short:**

> A `TreeMap` is a `Map` that keeps its keys **sorted** using a **Red-Black Tree**, ensuring `O(log n)` access and natural or custom ordering. It’s ideal when **sorted data and range queries** are needed.
