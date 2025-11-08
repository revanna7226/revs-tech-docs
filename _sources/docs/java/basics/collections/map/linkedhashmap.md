# 🧭 LinkedHashMap

`LinkedHashMap` is a **HashMap** that maintains a **linked list** of its entries.
It preserves **insertion order** or **access order** (if configured), unlike `HashMap`, which is unordered.

### ➤ Class Declaration:

```java
public class LinkedHashMap<K,V> extends HashMap<K,V> implements Map<K,V>
```

So `LinkedHashMap` **inherits** all behavior of `HashMap` but adds **ordering** through a **doubly-linked list**.

---

# 🧩 2. Key Features

| Feature             | Description                                                                |
| ------------------- | -------------------------------------------------------------------------- |
| **Ordering**        | Maintains predictable order of iteration — by **insertion** or **access**. |
| **Underlying DS**   | Combination of **Hash table + Doubly Linked List**.                        |
| **Performance**     | Same O(1) for `get()`, `put()`, `remove()` (like HashMap).                 |
| **Nulls**           | Allows one `null` key and multiple `null` values.                          |
| **Synchronization** | Not thread-safe (like HashMap).                                            |
| **Special Use**     | Often used for **caches** (especially LRU cache).                          |

---

# 🧠 3. Internal Data Structure

Internally, a `LinkedHashMap` uses:

```
Hash Table + Doubly Linked List
```

Each node (called `Entry` or `Node`) looks like this (simplified):

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before, after; // doubly linked list pointers
}
```

So each entry has:

- `key`, `value`, `hash`, `next` → from `HashMap` (for bucket)
- `before`, `after` → extra pointers to maintain insertion/access order

### 🔗 Structure visualization:

```
Hash Table (array of buckets)
     ↓
   [0] → (Node1) → (Node2)
   [1] → (Node3)
     ↓
   Doubly Linked List
   Head ⇄ Node1 ⇄ Node2 ⇄ Node3 ⇄ Tail
```

---

# ⚙️ 4. How it Maintains Order

`LinkedHashMap` maintains a **doubly linked list** of entries in the order they were inserted (or accessed).

Two ordering modes:

1. **Insertion Order** (default)
   Entries appear in the order they were put into the map.
2. **Access Order** (if `accessOrder=true` in constructor)
   Recently accessed (via `get()` or `put()`) entries move to the **end** of the list.
   → Used for implementing **LRU cache**.

### Example constructors:

```java
LinkedHashMap<Integer, String> map1 = new LinkedHashMap<>();
LinkedHashMap<Integer, String> map2 = new LinkedHashMap<>(16, 0.75f, true); // access order
```

---

# 🧩 5. Constructors

```java
LinkedHashMap()
LinkedHashMap(int initialCapacity)
LinkedHashMap(int initialCapacity, float loadFactor)
LinkedHashMap(int initialCapacity, float loadFactor, boolean accessOrder)
LinkedHashMap(Map<? extends K,? extends V> m)
```

The last parameter `accessOrder` controls order type:

- `false` → Insertion order (default)
- `true` → Access order

---

# 💡 6. Example 1 — Insertion Order

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class LinkedHashMapInsertionOrder {
    public static void main(String[] args) {
        Map<Integer, String> map = new LinkedHashMap<>();
        map.put(10, "Apple");
        map.put(30, "Banana");
        map.put(20, "Cherry");

        System.out.println("Insertion Order:");
        for (Map.Entry<Integer, String> e : map.entrySet()) {
            System.out.println(e.getKey() + " => " + e.getValue());
        }
    }
}
```

### 🧾 Output:

```
Insertion Order:
10 => Apple
30 => Banana
20 => Cherry
```

---

# 💡 7. Example 2 — Access Order

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class LinkedHashMapAccessOrder {
    public static void main(String[] args) {
        Map<Integer, String> map = new LinkedHashMap<>(16, 0.75f, true);
        map.put(10, "A");
        map.put(20, "B");
        map.put(30, "C");

        map.get(10); // access 10
        map.get(20); // access 20

        System.out.println("Access Order:");
        for (Map.Entry<Integer, String> e : map.entrySet()) {
            System.out.println(e.getKey() + " => " + e.getValue());
        }
    }
}
```

### 🧾 Output:

```
Access Order:
30 => C
10 => A
20 => B
```

Keys 10 and 20 moved to the **end** after access.

---

# ⚡ 8. Example 3 — LRU Cache (Remove Eldest Entry)

`LinkedHashMap` provides a **protected method**:

```java
protected boolean removeEldestEntry(Map.Entry<K,V> eldest)
```

You can override it to remove the oldest entry automatically (used for **LRU caches**).

### ✅ Example:

```java
import java.util.LinkedHashMap;
import java.util.Map;

public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int capacity;

    public LRUCache(int capacity) {
        super(capacity, 0.75f, true); // access order = true
        this.capacity = capacity;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
        return size() > capacity;
    }

    public static void main(String[] args) {
        LRUCache<Integer, String> cache = new LRUCache<>(3);
        cache.put(1, "A");
        cache.put(2, "B");
        cache.put(3, "C");
        cache.get(1);
        cache.put(4, "D"); // removes least recently used (key 2)

        System.out.println(cache);
    }
}
```

### 🧾 Output:

```
{3=C, 1=A, 4=D}
```

✅ Key `2` was least recently used → automatically removed.

---

# ⚙️ 9. Internal Implementation Flow (Simplified)

1. **Insertion**

   - Compute hash, find bucket (like HashMap).
   - Create new entry and insert at end of linked list (`tail`).

2. **Access (if accessOrder=true)**

   - Move accessed node to end of list.

3. **Iteration**

   - Iterates using `head → next` pointers (preserving order).

---

# 📚 10. Real JDK Snippet (Simplified)

From OpenJDK `LinkedHashMap.java` (trimmed for clarity):

```java
class LinkedHashMap<K,V> extends HashMap<K,V> {
    static class Entry<K,V> extends HashMap.Node<K,V> {
        Entry<K,V> before, after;
        Entry(int hash, K key, V value, Node<K,V> next) {
            super(hash, key, value, next);
        }
    }

    transient Entry<K,V> head;
    transient Entry<K,V> tail;
    final boolean accessOrder;

    public LinkedHashMap(int initialCapacity, float loadFactor, boolean accessOrder) {
        super(initialCapacity, loadFactor);
        this.accessOrder = accessOrder;
    }

    void afterNodeAccess(Node<K,V> e) {
        if (accessOrder) {
            Entry<K,V> last;
            if ((last = tail) != e) {
                Entry<K,V> p = (Entry<K,V>) e, b = p.before, a = p.after;
                p.after = null;
                if (b == null)
                    head = a;
                else
                    b.after = a;
                if (a != null)
                    a.before = b;
                else
                    last = b;
                if (last == null)
                    head = p;
                else {
                    p.before = last;
                    last.after = p;
                }
                tail = p;
            }
        }
    }
}
```

You can see how it rearranges the node pointers to maintain the order.

---

# 🧷 11. Summary Table

| Feature               | `HashMap`       | `LinkedHashMap`                  |
| --------------------- | --------------- | -------------------------------- |
| **Order maintained**  | ❌ No           | ✅ Yes (Insertion or Access)     |
| **Underlying DS**     | Hash table      | Hash table + Doubly linked list  |
| **Performance**       | O(1) average    | O(1) average                     |
| **Use case**          | Fast, unordered | Predictable iteration order      |
| **LRU cache support** | ❌ No           | ✅ Yes (via `removeEldestEntry`) |
| **Null keys/values**  | ✅ Yes          | ✅ Yes                           |
| **Thread-safe**       | ❌ No           | ❌ No                            |

---

# 🏁 12. Summary

**`LinkedHashMap`** =

> “`HashMap` + Doubly Linked List = predictable iteration order + O(1) lookups.”

### ✅ Advantages:

- Maintains predictable order
- Great for cache-like structures (LRU)
- Same fast performance as `HashMap`

### ⚠️ Disadvantages:

- Slightly higher memory usage (extra pointers)
- Not thread-safe (use `Collections.synchronizedMap()` or `ConcurrentHashMap` if needed)
