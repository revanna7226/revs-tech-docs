# 🧭 HashMap

## Constructor Variants

```java
// The default initial capacity is 16
HashMap<K, V> map = new HashMap<>();

// 32, Always a power of 2 for efficient hashing and indexing.
HashMap<K, V> map = new HashMap<>(int initialCapacity);

// The load factor determines when to resize the map.
// Default load factor = 0.75
HashMap<K, V> map = new HashMap<>(int initialCapacity, float loadFactor);

```

## 1️⃣ Overview of HashMap

A **`HashMap`** in Java is a data structure that implements the **`Map` interface**, storing key-value pairs. It allows **O(1)** average-time complexity for **insertion**, **deletion**, and **lookup**.

**Key points:**

- It uses **hashing** to locate buckets.
- Allows one **`null` key** and multiple **`null` values**.
- **Not synchronized** → not thread-safe.
- Order of keys is **not guaranteed**.

---

## 2️⃣ Internal Structure

Internally, a `HashMap` uses an **array of buckets** (called `table`).
Each bucket is a **linked list** (or **red-black tree** in Java 8+ for performance).

### Basic structure:

```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
        this.hash = hash;
        this.key = key;
        this.value = value;
        this.next = next;
    }
}
```

**Fields:**

- `hash` → stores precomputed hash value for efficiency.
- `key`, `value` → key–value pair.
- `next` → reference to the next node in the same bucket (handles collisions).

---

## 3️⃣ Hashing and Bucket Index Calculation

Each key’s `hashCode()` is processed to compute a **spread hash** — reducing collisions.

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

**Why XOR with `>>>16`?**

- Ensures that both high and low bits of hash influence the final bucket index.
- Improves uniform key distribution.

**Bucket index formula:**

```java
index = (n - 1) & hash
```

Where `n` is the current table length (always a **power of two**).

Example:

```java
int n = 16; // table length
int hash = 123456;
int index = (n - 1) & hash; // ensures index in range 0–15
```

---

## 4️⃣ How `put()` Works

### Steps:

1. Compute `hash` of the key.
2. Find **bucket index** → `(n - 1) & hash`.
3. If bucket is empty → create a new node.
4. If not empty → traverse linked list:

   - If existing key matches → replace value.
   - Else append new node at the end.

5. If size exceeds `threshold` → **resize the table**.

### Source-level logic (simplified):

```java
public V put(K key, V value) {
    int hash = hash(key);
    int index = (table.length - 1) & hash;

    for (Node<K,V> e = table[index]; e != null; e = e.next) {
        if (e.hash == hash && (e.key == key || e.key.equals(key))) {
            V old = e.value;
            e.value = value; // Replace existing
            return old;
        }
    }

    // Insert new node
    Node<K,V> newNode = new Node<>(hash, key, value, table[index]);
    table[index] = newNode;
    size++;

    if (size > threshold)
        resize();

    return null;
}
```

---

## 5️⃣ How `get()` Works

### Steps:

1. Compute `hash` and bucket index.
2. Traverse the linked list (or tree) in that bucket.
3. Return value if key matches; else return `null`.

```java
public V get(Object key) {
    int hash = hash(key);
    int index = (table.length - 1) & hash;
    for (Node<K,V> e = table[index]; e != null; e = e.next) {
        if (e.hash == hash && (e.key == key || e.key.equals(key))) {
            return e.value;
        }
    }
    return null;
}
```

---

## 6️⃣ Collision Handling

When two keys map to the same index (hash collision):

- Nodes are chained together via a **linked list** (chaining).
- In Java 8+, if the chain length > **8**, it is converted into a **red–black tree** for better performance.

---

## 7️⃣ Resizing and Rehashing

`HashMap` resizes when:

```
size > capacity × loadFactor
```

Default:

- capacity = 16
- loadFactor = 0.75
  → threshold = 12

### Resize process:

1. Double the capacity.
2. Recompute bucket index for all existing entries.
3. Move nodes to new buckets.

```java
void resize() {
    Node<K,V>[] oldTable = table;
    int newCapacity = oldTable.length * 2;
    Node<K,V>[] newTable = new Node[newCapacity];

    for (Node<K,V> node : oldTable) {
        while (node != null) {
            Node<K,V> next = node.next;
            int index = (newCapacity - 1) & node.hash;
            node.next = newTable[index];
            newTable[index] = node;
            node = next;
        }
    }

    table = newTable;
    threshold = (int)(newCapacity * loadFactor);
}
```

---

## 8️⃣ Simplified Implementation Example

Here’s a minimal working `HashMap` for educational understanding 👇

```java
import java.util.Objects;

public class SimpleHashMap<K, V> {
    static class Node<K, V> {
        final int hash;
        final K key;
        V value;
        Node<K, V> next;
        Node(int hash, K key, V value, Node<K, V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
    }

    private Node<K, V>[] table;
    private int size;
    private int threshold;
    private final float loadFactor;
    private static final int DEFAULT_CAPACITY = 16;
    private static final float DEFAULT_LOAD_FACTOR = 0.75f;

    @SuppressWarnings("unchecked")
    public SimpleHashMap() {
        this.table = (Node<K, V>[]) new Node[DEFAULT_CAPACITY];
        this.loadFactor = DEFAULT_LOAD_FACTOR;
        this.threshold = (int) (DEFAULT_CAPACITY * loadFactor);
    }

    private int hash(Object key) {
        int h = (key == null) ? 0 : key.hashCode();
        return h ^ (h >>> 16);
    }

    public V put(K key, V value) {
        int h = hash(key);
        int index = (table.length - 1) & h;

        for (Node<K, V> e = table[index]; e != null; e = e.next) {
            if (e.hash == h && Objects.equals(e.key, key)) {
                V oldValue = e.value;
                e.value = value;
                return oldValue;
            }
        }

        Node<K, V> newNode = new Node<>(h, key, value, table[index]);
        table[index] = newNode;
        size++;

        if (size > threshold) resize();
        return null;
    }

    @SuppressWarnings("unchecked")
    private void resize() {
        Node<K, V>[] oldTable = table;
        int newCap = oldTable.length * 2;
        Node<K, V>[] newTable = (Node<K, V>[]) new Node[newCap];

        for (Node<K, V> node : oldTable) {
            while (node != null) {
                Node<K, V> next = node.next;
                int idx = (newCap - 1) & node.hash;
                node.next = newTable[idx];
                newTable[idx] = node;
                node = next;
            }
        }

        table = newTable;
        threshold = (int) (newCap * loadFactor);
    }

    public V get(Object key) {
        int h = hash(key);
        int index = (table.length - 1) & h;

        for (Node<K, V> e = table[index]; e != null; e = e.next) {
            if (e.hash == h && Objects.equals(e.key, key))
                return e.value;
        }
        return null;
    }

    public int size() { return size; }
}
```

### Example Usage:

```java
public class Main {
    public static void main(String[] args) {
        SimpleHashMap<String, Integer> map = new SimpleHashMap<>();
        map.put("A", 1);
        map.put("B", 2);
        map.put("C", 3);

        System.out.println(map.get("A")); // 1
        System.out.println(map.get("B")); // 2

        map.put("A", 10); // Update
        System.out.println(map.get("A")); // 10
    }
}
```

---

## 9️⃣ Treeification in Java 8+

If too many keys land in the same bucket (≥ 8 nodes), the linked list is replaced by a **red–black tree** to maintain O(log n) lookup time.

Conditions:

- Bucket size ≥ 8
- Table capacity ≥ 64

If table size is smaller, resizing happens instead of treeification.

---

## 🔟 Complexity Analysis

| Operation  | Average Case | Worst Case (Pre-Java 8) | Worst Case (Java 8+) |
| ---------- | ------------ | ----------------------- | -------------------- |
| `put()`    | O(1)         | O(n)                    | O(log n)             |
| `get()`    | O(1)         | O(n)                    | O(log n)             |
| `remove()` | O(1)         | O(n)                    | O(log n)             |

---

## 1️⃣1️⃣ Important Notes

- **Null key** allowed → always stored in bucket `index = 0`.
- **HashMap is not thread-safe** → use `ConcurrentHashMap` for concurrency.
- Always override both `hashCode()` and `equals()` properly.
- Table size is always a **power of 2** for efficient bit masking.
- Frequent resizing can degrade performance → set initial capacity wisely.

---

### 🔍 Summary Diagram

```
table[0] → [Node(key1, val1)] → [Node(key2, val2)]
table[1] → null
table[2] → [Node(key3, val3)]
...
```

---

## ✅ Final Thoughts

`HashMap` in Java is a **powerful, efficient, and well-optimized** data structure that leverages:

- Bit manipulation (`&`, `>>>`)
- Load factor tuning
- Dynamic resizing
- Balanced trees for high-collision buckets

This design achieves **constant-time average complexity** while remaining **memory-efficient**.
