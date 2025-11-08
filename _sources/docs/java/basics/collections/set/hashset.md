# 🧩 1️⃣ What is `HashSet` in Java?

### ➤ Definition:

A **`HashSet`** is a **collection** in Java that:

- Stores **unique elements only** (no duplicates)
- Has **no guaranteed order of elements**
- Provides **constant time performance (O(1))** for basic operations like `add()`, `remove()`, and `contains()`

It’s part of the **Java Collections Framework** and implements the `Set` interface.

---

### 📦 Class Declaration (from JDK source)

```java
public class HashSet<E> extends AbstractSet<E>
        implements Set<E>, Cloneable, java.io.Serializable {
    private transient HashMap<E,Object> map;
    private static final Object PRESENT = new Object();
}
```

✅ So internally, a `HashSet` is actually built on top of a **`HashMap`**!

---

## 🧠 2️⃣ Data Structure used in `HashSet`

### 📚 Under the Hood:

- Internally, `HashSet` uses a **`HashMap`** to store its elements.
- Each element of the set is stored as a **key** in the `HashMap`.
- The map’s value is a **dummy constant object** (often called `PRESENT`).

This is how uniqueness is guaranteed:
👉 A `HashMap` **cannot have duplicate keys**, so neither can a `HashSet`.

---

### 🔩 Internal Data Structure Summary

| Concept            | `HashSet`          | `HashMap` used internally                 |
| ------------------ | ------------------ | ----------------------------------------- |
| Storage            | Elements only      | key = element, value = dummy object       |
| Duplicate handling | Disallowed         | Keys are unique                           |
| Null element       | Allowed (only one) | Because HashMap allows one null key       |
| Order              | Unordered          | Hashing-based buckets                     |
| Backing structure  | HashMap            | Hash table + linked list/tree for buckets |

---

## ⚙️ 3️⃣ How `HashSet` Works Internally

Let’s look step-by-step 👇

### 🏗️ Step 1: Creating a `HashSet`

```java
HashSet<String> set = new HashSet<>();
```

When you do this:

- A new **empty `HashMap`** is created internally with default capacity `16` and load factor `0.75f`.

---

### 🧾 Step 2: Adding Elements

```java
set.add("Apple");
```

Internally:

```java
map.put("Apple", PRESENT);
```

If `"Apple"` is not already present as a key, it is inserted:

- The hash code of `"Apple"` is computed.
- Index = `(hash & (n - 1))` → finds the bucket.
- If the bucket is empty, a new node is placed.
- If the bucket already has elements (collision), it’s added as a linked node (or tree node in Java 8+).

If `"Apple"` already exists, the map ignores the insertion (so duplicates are not added).

---

### 🧾 Step 3: Checking Existence

```java
set.contains("Apple");
```

Internally:

```java
map.containsKey("Apple");
```

---

### 🧾 Step 4: Removing Elements

```java
set.remove("Apple");
```

Internally:

```java
map.remove("Apple");
```

---

### ✅ Step 5: Iteration

When you iterate a `HashSet`, it just iterates over the **keys of the HashMap**:

```java
for (String s : set) {
    System.out.println(s);
}
```

Internally calls:

```java
map.keySet().iterator();
```

---

## 💻 4️⃣ Example Code — Behavior & Internal Flow

```java
import java.util.HashSet;

public class HashSetExample {
    public static void main(String[] args) {
        HashSet<String> fruits = new HashSet<>();

        fruits.add("Apple");
        fruits.add("Banana");
        fruits.add("Mango");
        fruits.add("Apple"); // duplicate ignored

        System.out.println("Fruits: " + fruits);

        System.out.println("Contains Mango? " + fruits.contains("Mango"));
        fruits.remove("Banana");
        System.out.println("After removing Banana: " + fruits);

        // Iterating
        for (String f : fruits) {
            System.out.println("Fruit: " + f);
        }
    }
}
```

### 🧾 Output (order may vary)

```
Fruits: [Apple, Banana, Mango]
Contains Mango? true
After removing Banana: [Apple, Mango]
Fruit: Apple
Fruit: Mango
```

---

## 🧮 5️⃣ Internal Working Mini Model (Pseudocode)

Let’s look at the constructor and `add()` flow in simplified form:

```java
public class HashSet<E> {
    private transient HashMap<E, Object> map;
    private static final Object PRESENT = new Object();

    public HashSet() {
        map = new HashMap<>();
    }

    public boolean add(E e) {
        return map.put(e, PRESENT) == null;
    }

    public boolean remove(Object o) {
        return map.remove(o) == PRESENT;
    }

    public boolean contains(Object o) {
        return map.containsKey(o);
    }

    public int size() {
        return map.size();
    }
}
```

🧠 **Key takeaway:**
👉 `HashSet` is just a wrapper over `HashMap` where **keys = elements** and **value = dummy object**.

---

## ⚖️ 6️⃣ Load Factor and Capacity

Just like `HashMap`:

- **Initial Capacity:** 16
- **Load Factor:** 0.75

When the number of elements exceeds `16 × 0.75 = 12`, the set **resizes** its internal table (rehash).

---

## 🔍 7️⃣ Performance

| Operation  | Average Time Complexity | Worst Case                                 |
| ---------- | ----------------------- | ------------------------------------------ |
| add()      | O(1)                    | O(n) (if all elements hash to same bucket) |
| remove()   | O(1)                    | O(n)                                       |
| contains() | O(1)                    | O(n)                                       |

_Note:_ Since Java 8, buckets may convert from linked list → red-black tree after threshold (8 entries per bucket), improving worst-case from **O(n)** to **O(log n)**.

---

## 🧭 8️⃣ Differences between HashSet and other Sets

| Feature            | HashSet   | LinkedHashSet             | TreeSet      |
| ------------------ | --------- | ------------------------- | ------------ |
| Ordering           | No        | Maintains insertion order | Sorted order |
| Null allowed       | Yes (one) | Yes (one)                 | No           |
| Backed by          | HashMap   | LinkedHashMap             | TreeMap      |
| Performance        | O(1)      | O(1)                      | O(log n)     |
| Comparator support | No        | No                        | Yes          |

---

## 🚀 9️⃣ Example – Handling Duplicates Automatically

```java
import java.util.HashSet;
import java.util.Arrays;

public class RemoveDuplicatesExample {
    public static void main(String[] args) {
        Integer[] numbers = {1, 2, 3, 2, 4, 1, 5};
        HashSet<Integer> unique = new HashSet<>(Arrays.asList(numbers));
        System.out.println("Unique numbers: " + unique);
    }
}
```

✅ Output:

```
Unique numbers: [1, 2, 3, 4, 5]
```

---

## 🧷 10️⃣ Summary

| Concept                | Description                                         |
| ---------------------- | --------------------------------------------------- |
| **Class**              | `java.util.HashSet`                                 |
| **Implements**         | `Set`, `Cloneable`, `Serializable`                  |
| **Underlying DS**      | `HashMap`                                           |
| **Stores duplicates?** | ❌ No                                               |
| **Allows null?**       | ✅ One null                                         |
| **Thread-safe?**       | ❌ No                                               |
| **Best for**           | Fast lookups, membership checks, unique collections |
| **Average complexity** | `O(1)` for add, remove, contains                    |

---

✅ **In one line:**

> `HashSet` is a `Set` implementation backed by a `HashMap`, storing unique elements with O(1) performance using hashing.
