# LinkedHashSet

`LinkedHashSet` is a concrete `Set` implementation in Java that **preserves iteration order** while keeping `HashSet`’s O(1) average performance characteristics. It’s simple in concept but useful in practice when you need uniqueness **and** predictable iteration order.

Below I’ll explain:

- What `LinkedHashSet` is and when to use it
- The internal data structure and how it’s implemented (important details)
- Performance and semantics (nulls, thread-safety, fail-fast)
- Real code examples and a small educational sketch of the implementation

---

# What is `LinkedHashSet`?

- `LinkedHashSet<E>` implements `Set<E>` and guarantees that iteration over the set will be in **insertion order** (the order in which elements were added).
- It is backed internally by a `LinkedHashMap<E, Object>`.
- It **disallows duplicates** (like all `Set`s) and **allows one `null`** element.
- It is **not synchronized** (not thread-safe) — use concurrent wrappers or concurrent collections for multi-threaded use.

Typical use-cases:

- Maintain insertion order while removing duplicates (e.g., preserving first-seen order).
- Building simple caches where insertion-order eviction is acceptable (for LRU you’d use `LinkedHashMap` access-order instead).

---

# Internal data structure & implementation (how it works)

## Backing store

`LinkedHashSet` is effectively a thin wrapper around `LinkedHashMap`:

```java
public class LinkedHashSet<E> extends HashSet<E> implements Set<E>, Cloneable, java.io.Serializable {
    public LinkedHashSet() {
        super(new LinkedHashMap<>());
    }
    // other constructors delegate similarly
}
```

So each element in the `LinkedHashSet` is stored as a **key** in the underlying `LinkedHashMap`, with a dummy value (like `PRESENT` in `HashSet`).

## How `LinkedHashMap` preserves order

`LinkedHashMap` extends `HashMap` and maintains a **doubly-linked list** that connects all entries in the order of insertion (or access, if configured). Internally:

- Each map entry/node stores `before` and `after` references (previous and next entry in list).
- When a new entry is inserted, it’s appended to the end of that linked list.
- When an entry is removed, the `before`/`after` neighbors are relinked.
- Iterators traverse entries by walking that linked list — this gives predictable order without reorganizing the hash table.

Important notes:

- `LinkedHashSet` **preserves insertion order only** because it uses the default `LinkedHashMap` mode (insertion-order). `LinkedHashMap` itself can be configured for **access-order** (useful for LRU caches), but `LinkedHashSet` does not expose that directly.
- The hash table (from `HashMap`) still provides O(1) lookup; the linked list provides ordering.

## Fail-fast iterators

Iterators returned by `LinkedHashSet` (via the underlying `LinkedHashMap`) are **fail-fast** — they check modification count (`modCount`) and throw `ConcurrentModificationException` if the set is structurally modified during iteration (except by the iterator’s own `remove()`).

---

# Constructors (common ones)

```java
LinkedHashSet<E> set = new LinkedHashSet<>();                    // default initial capacity & load factor
LinkedHashSet<E> set = new LinkedHashSet<>(collection);          // from another collection (keeps its iteration order)
LinkedHashSet<E> set = new LinkedHashSet<>(initialCapacity);     // specify capacity
LinkedHashSet<E> set = new LinkedHashSet<>(initialCapacity, loadFactor);
```

When constructed from a `Collection`, elements are added in the order the collection’s iterator returns them.

---

# Time complexity

|  Operation | Average time              |
| ---------: | ------------------------- |
|      `add` | O(1)                      |
| `contains` | O(1)                      |
|   `remove` | O(1)                      |
|  Iteration | O(n) (in insertion order) |

Worst-case is higher if many collisions occur, but since Java 8 `HashMap` bins treeify, worst-case becomes O(log n) for pathological collisions.

---

# Code examples

### Basic usage & insertion-order guarantee

```java
import java.util.LinkedHashSet;

public class LHSBasic {
    public static void main(String[] args) {
        LinkedHashSet<String> set = new LinkedHashSet<>();
        set.add("first");
        set.add("second");
        set.add("third");
        set.add("second"); // duplicate ignored

        // iteration preserves insertion order
        for (String s : set) {
            System.out.println(s);
        }
    }
}
```

Output:

```
first
second
third
```

### Create from another collection (keeps order)

```java
import java.util.*;

public class FromList {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("a","b","c","a","b");
        LinkedHashSet<String> set = new LinkedHashSet<>(list);
        System.out.println(set); // [a, b, c]
    }
}
```

### Use-case: remove duplicates while preserving first-seen order

```java
String[] seq = {"apple", "banana", "apple", "orange", "banana"};
LinkedHashSet<String> uniq = new LinkedHashSet<>(Arrays.asList(seq));
System.out.println(uniq); // [apple, banana, orange]
```

### Iteration + fail-fast example

```java
LinkedHashSet<Integer> set = new LinkedHashSet<>(Arrays.asList(1,2,3));
for (Integer i : set) {
    if (i == 2) set.remove(i); // will throw ConcurrentModificationException
}
```

Correct approach: use `Iterator.remove()` while iterating.

---

# Small educational sketch (simplified) — how LinkedHashSet delegates

This is a tiny illustrative sketch (not production code):

```java
public class SimpleLinkedHashSet<E> {
    private static final Object PRESENT = new Object();
    private final LinkedHashMap<E,Object> map;

    public SimpleLinkedHashSet() {
        this.map = new LinkedHashMap<>();
    }

    public boolean add(E e) {
        return map.put(e, PRESENT) == null;
    }

    public boolean contains(E e) {
        return map.containsKey(e);
    }

    public boolean remove(E e) {
        return map.remove(e) == PRESENT;
    }

    public int size() {
        return map.size();
    }

    public Iterator<E> iterator() {
        return map.keySet().iterator(); // iterates in insertion order
    }
}
```

Key point: `LinkedHashSet` _is_ this idea in the JDK — a `HashSet` backed by `LinkedHashMap` instead of a plain `HashMap`.

---

# Differences vs related Set implementations

- `HashSet` — does _not_ guarantee iteration order. Backed by `HashMap`. Use if order does not matter and you want max performance.
- `LinkedHashSet` — preserves insertion order. Backed by `LinkedHashMap`. Slightly more memory overhead (linked-list pointers) than `HashSet`.
- `TreeSet` — keeps elements sorted (via `Comparable` or `Comparator`) and operations are O(log n). Use if you need sorted order.
- `CopyOnWriteArraySet` / `ConcurrentSkipListSet` — concurrency-friendly sets (different tradeoffs).

---

# Practical notes & pitfalls

- `LinkedHashSet` **allows one `null`** element. `LinkedHashMap` allows one `null` key; `LinkedHashSet` inherits that.
- Not thread-safe. For concurrent use, either:

  - Use `Collections.synchronizedSet(new LinkedHashSet<>(...))` (wrap and synchronize while iterating), or
  - Use concurrent collections (but there is no direct concurrent LinkedHashSet; you may use a `ConcurrentLinkedQueue` + `ConcurrentHashMap` combo or `ConcurrentSkipListSet` if sorted behavior helps).

- Higher memory than `HashSet` (the linked list pointers per entry).
- If you need LRU eviction (remove eldest on access), use `LinkedHashMap` in **access-order** mode with `removeEldestEntry` override (not `LinkedHashSet`).

Example LRU cache (brief):

```java
class LRUCache<K,V> extends LinkedHashMap<K,V> {
    private final int max;

    LRUCache(int max) {
        super(16, 0.75f, true); // accessOrder = true
        this.max = max;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
        return size() > max;
    }
}
```

---

# Summary — what to remember

- `LinkedHashSet` = `Set` that preserves insertion order, backed by `LinkedHashMap`.
- It gives O(1) average performance for add/contains/remove while guaranteeing predictable iteration order.
- Not thread-safe; iterators are fail-fast; allows one `null`.
- Use when you need a unique collection with stable iteration order (e.g., deduplicating while preserving first-seen order).
