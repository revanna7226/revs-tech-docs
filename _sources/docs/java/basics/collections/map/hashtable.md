# 🧩 1️⃣ What is `Hashtable` in Java?

`Hashtable` is a **legacy class** in Java that implements the **Map** interface and stores data as **key–value pairs**.

It was introduced in **JDK 1.0**, even _before_ the Java Collections Framework (JCF) — and later adapted (in Java 1.2) to fit into it by implementing the `Map<K,V>` interface.

---

## 🔍 Key Characteristics

| Feature             | Description                                                                              |
| ------------------- | ---------------------------------------------------------------------------------------- |
| **Package**         | `java.util`                                                                              |
| **Implements**      | `Map<K,V>`, `Cloneable`, `Serializable`                                                  |
| **Thread Safety**   | ✅ Yes — all methods are `synchronized`                                                  |
| **Nulls allowed?**  | ❌ No — neither `null` key nor `null` value                                              |
| **Iteration order** | Unordered (no guarantee)                                                                 |
| **Fail-fast**       | Yes — throws `ConcurrentModificationException` if structurally modified during iteration |

---

# 🧱 2️⃣ Internal Structure

### Conceptually, a `Hashtable` consists of:

- An **array of buckets** (`Entry[] table`)
- Each **bucket** contains a **linked list** (chain) of entries
- Each **Entry** (node) stores:

  - `hash` → hash of the key
  - `key` → the key object
  - `value` → the associated value
  - `next` → reference to next Entry in the chain

```
table[0] → [hash, key, value, next] → [hash, key, value, next] → null
table[1] → null
table[2] → [hash, key, value, next]
```

---

# ⚙️ 3️⃣ How `Hashtable` Works Internally

Let’s go through the core operations 👇

---

## (A) **Inserting (put method)**

### ✅ Algorithm:

1. Compute the **hash** of the key using `key.hashCode()`.
2. Calculate the **bucket index**:

   ```java
   int index = (hash & 0x7FFFFFFF) % table.length;
   ```

3. Traverse the bucket:

   - If key already exists → update its value.
   - Else → create a new `Entry` and insert it at the start of the chain.

4. If `size > threshold`, **rehash** (resize the table).

### 🧠 Important:

- The whole `put()` method is `synchronized` → only one thread can modify the table at a time.

### Example (simplified source from JDK 8):

```java
public synchronized V put(K key, V value) {
    if (value == null)
        throw new NullPointerException();

    int hash = key.hashCode();
    int index = (hash & 0x7FFFFFFF) % table.length;

    for (Entry<K,V> e = table[index]; e != null; e = e.next) {
        if (e.hash == hash && e.key.equals(key)) {
            V old = e.value;
            e.value = value;
            return old;
        }
    }

    addEntry(hash, key, value, index);
    return null;
}

private void addEntry(int hash, K key, V value, int index) {
    Entry<?,?> tab[] = table;
    if (count >= threshold) {
        // Rehash the table if the threshold is exceeded
        rehash();

        tab = table;
        hash = key.hashCode();
        index = (hash & 0x7FFFFFFF) % tab.length;
    }

    // Creates the new entry.
    @SuppressWarnings("unchecked")
    Entry<K,V> e = (Entry<K,V>) tab[index];
    tab[index] = new Entry<>(hash, key, value, e);
    count++;
    modCount++;
}
```

---

## (B) **Retrieving (get method)**

### ✅ Algorithm:

1. Compute hash and index (same formula as `put()`).
2. Traverse the linked list at `table[index]`.
3. If matching key found → return its value.
4. Else → return `null`.

```java
public synchronized V get(Object key) {
    int hash = key.hashCode();
    int index = (hash & 0x7FFFFFFF) % table.length;
    for (Entry<K,V> e = table[index]; e != null; e = e.next) {
        if (e.hash == hash && e.key.equals(key))
            return e.value;
    }
    return null;
}
```

Again — `synchronized` ensures thread-safety.

---

## (C) **Rehashing (Resizing)**

When the number of elements exceeds the **threshold**
(= capacity × loadFactor), the table’s capacity doubles (roughly).

```java
protected void rehash() {
    int oldCapacity = table.length;
    Entry<K,V>[] oldTable = table;

    int newCapacity = oldCapacity * 2 + 1;
    Entry<K,V>[] newTable = new Entry[newCapacity];

    threshold = (int)(newCapacity * loadFactor);
    table = newTable;

    for (int i = oldCapacity; i-- > 0; ) {
        for (Entry<K,V> old = oldTable[i]; old != null; ) {
            Entry<K,V> e = old;
            old = old.next;
            int index = (e.hash & 0x7FFFFFFF) % newCapacity;
            e.next = newTable[index];
            newTable[index] = e;
        }
    }
}
```

🧠 Note: During rehashing, all entries are redistributed based on the new table size.

---

# 🧵 4️⃣ Thread Safety

`Hashtable` achieves thread safety by **synchronizing all its public methods** on the instance (`this`).

Example:

```java
public synchronized V put(K key, V value)
public synchronized V get(Object key)
public synchronized V remove(Object key)
```

✅ This guarantees that **only one thread** can modify or read the table at any given moment.

❌ However, this also means **poor performance** in highly concurrent environments — because even read operations block each other.

---

# 🧮 5️⃣ Default Values

| Parameter            | Default Value |
| -------------------- | ------------- |
| **Initial Capacity** | 11            |
| **Load Factor**      | 0.75          |
| **Threshold**        | 8 (11 × 0.75) |

You can customize these values when constructing a `Hashtable`.

Example:

```java
Hashtable<String, Integer> table = new Hashtable<>(20, 0.5f);
```

---

# 📊 6️⃣ Example Usage

```java
import java.util.Hashtable;

public class HashtableExample {
    public static void main(String[] args) {
        Hashtable<String, Integer> marks = new Hashtable<>();

        marks.put("Alice", 90);
        marks.put("Bob", 85);
        marks.put("Charlie", 95);

        System.out.println("Alice: " + marks.get("Alice"));
        System.out.println("All entries: " + marks);
    }
}
```

**Output:**

```
Alice: 90
All entries: {Charlie=95, Bob=85, Alice=90}
```

---

# ⚔️ 7️⃣ Difference Between Hashtable, HashMap, and ConcurrentHashMap

| Feature         | `Hashtable`                 | `HashMap`      | `ConcurrentHashMap`              |
| --------------- | --------------------------- | -------------- | -------------------------------- |
| Introduced      | Java 1.0                    | Java 1.2       | Java 1.5                         |
| Thread-safe     | ✅ Yes (synchronized)       | ❌ No          | ✅ Yes (fine-grained locks)      |
| Allows nulls    | ❌ No                       | ✅ Yes         | ❌ No                            |
| Synchronization | Entire map (coarse-grained) | None           | Segment-level or per-bin locking |
| Performance     | Slow (global lock)          | Fast (no lock) | Very fast under concurrency      |
| Legacy          | Yes                         | No             | No                               |
| Iterator        | Fail-fast                   | Fail-fast      | Weakly consistent                |

---

# ⚙️ 8️⃣ Summary

| Property             | `Hashtable` Details                                       |
| -------------------- | --------------------------------------------------------- |
| **Purpose**          | Legacy synchronized hash-based Map                        |
| **Implementation**   | Array of buckets (linked lists)                           |
| **Thread-safety**    | Achieved via `synchronized` methods                       |
| **Null keys/values** | Not allowed                                               |
| **Rehashing**        | Doubles capacity + 1 when threshold exceeded              |
| **Performance**      | O(1) average lookup/insertion (but slower due to locking) |
| **Replacement**      | `ConcurrentHashMap` (modern, efficient alternative)       |

---

# 🧠 Quick Analogy:

Think of `Hashtable` as an **old, locked filing cabinet**:

- Only one person (thread) can open it at a time.
- Everything inside is safe.
- But if many people try to access it — they all have to wait.

Modern replacement (`ConcurrentHashMap`) uses **many small lockers** — allowing multiple people to work at once.
