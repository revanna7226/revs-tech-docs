# HashTable

## 🧩 **1. What is a HashTable?**

A **HashTable** is a **data structure** that stores **key-value pairs**.
It allows **fast access**, **insertion**, and **deletion** operations — typically **O(1)** on average.

---

## ⚙️ **2. Concept — How It Works Internally**

The HashTable uses a technique called **hashing**.

### Steps:

1. **Key** → is passed through a **hash function**.
2. The hash function generates an **index (hash code)** in an internal array (called a **bucket array**).
3. The **key-value pair** is stored in that bucket.
4. If **two keys hash to the same index**, a **collision** occurs, which is resolved using **chaining** (linked list or similar).

---

## 🧠 **3. Characteristics of Java’s Hashtable**

| Feature                      | Description                                     |
| ---------------------------- | ----------------------------------------------- |
| **Package**                  | `java.util.Hashtable`                           |
| **Implements**               | `Map<K,V>` interface                            |
| **Thread-Safe**              | ✅ Yes — synchronized (safe for multithreading) |
| **Allows null keys/values?** | ❌ No                                           |
| **Performance**              | Slower than `HashMap` due to synchronization    |

---

## 💡 **4. Basic Example of Hashtable in Java**

```java
import java.util.Hashtable;

public class HashTableExample {
    public static void main(String[] args) {
        // Create a Hashtable
        Hashtable<Integer, String> table = new Hashtable<>();

        // Add key-value pairs
        table.put(1, "Apple");
        table.put(2, "Banana");
        table.put(3, "Cherry");

        // Access elements
        System.out.println("Value for key 2: " + table.get(2));

        // Check if key exists
        System.out.println("Contains key 3? " + table.containsKey(3));

        // Remove an element
        table.remove(1);

        System.out.println("After removal: " + table);

        // Iterate over Hashtable
        for (Integer key : table.keySet()) {
            System.out.println("Key: " + key + ", Value: " + table.get(key));
        }
    }
}
```

### 🧾 **Output:**

```
Value for key 2: Banana
Contains key 3? true
After removal: {3=Cherry, 2=Banana}
Key: 3, Value: Cherry
Key: 2, Value: Banana
```

---

## 🧩 **5. How Hashtable Stores Data Internally**

Let’s visualize:

```text
index  | key  | value
---------------------
  0    |  -   |  -
  1    |  3   | Cherry
  2    |  2   | Banana
  3    |  -   |  -
  4    |  1   | Apple
```

A **hash function** maps each key to an index:

```java
int index = key.hashCode() % table.length;
```

If two keys hash to the same index, Java handles it via **chaining** (a linked list in that bucket).

---

## ⚔️ **6. Collision Handling**

**Example:**
If two keys generate the same hash code, they go in the same bucket (a linked list).
When retrieving, the HashTable checks the keys using `.equals()` to find the right value.

---

## 🧵 **7. Thread Safety**

`Hashtable` methods are **synchronized** — meaning only one thread can access it at a time.

✅ Safe for multithreaded use.
❌ But slower than `HashMap` (which is unsynchronized).

If you need a modern thread-safe map, prefer:

```java
ConcurrentHashMap<K, V>
```

---

## ⚡ **8. Common Hashtable Methods**

| Method                        | Description                   |
| ----------------------------- | ----------------------------- |
| `put(K key, V value)`         | Add or replace key-value pair |
| `get(Object key)`             | Retrieve value                |
| `remove(Object key)`          | Remove entry                  |
| `containsKey(Object key)`     | Check if key exists           |
| `containsValue(Object value)` | Check if value exists         |
| `isEmpty()`                   | Check if empty                |
| `size()`                      | Number of entries             |
| `clear()`                     | Remove all entries            |
| `keys()`                      | Returns Enumeration of keys   |
| `elements()`                  | Returns Enumeration of values |

---

## 👨🏻‍💻 **9. Custom HashTable Implementation**

```java

```

## 💻 **10. Example: Hashtable for Counting Frequencies**

```java
import java.util.Hashtable;

public class FrequencyCounter {
    public static void main(String[] args) {
        String text = "apple banana apple cherry banana apple";
        String[] words = text.split(" ");

        Hashtable<String, Integer> frequency = new Hashtable<>();

        for (String word : words) {
            frequency.put(word, frequency.getOrDefault(word, 0) + 1);
        }

        System.out.println("Word Frequencies:");
        for (String key : frequency.keySet()) {
            System.out.println(key + " → " + frequency.get(key));
        }
    }
}
```

**Output:**

```
Word Frequencies:
banana → 2
apple → 3
cherry → 1
```

---

## 🧮 **11. Hashtable vs HashMap**

| Feature                 | Hashtable           | HashMap                               |
| ----------------------- | ------------------- | ------------------------------------- |
| Thread Safety           | ✅ Yes              | ❌ No                                 |
| Allows null keys/values | ❌ No               | ✅ Yes (1 null key, many null values) |
| Performance             | Slower              | Faster                                |
| Introduced In           | JDK 1.0             | JDK 1.2                               |
| Synchronized            | Yes (global)        | No                                    |
| Best Used For           | Multi-threaded apps | Single-threaded apps                  |

---

## 🧠 **12. DSA View — Time Complexity**

| Operation | Average Case | Worst Case |
| --------- | ------------ | ---------- |
| Insertion | O(1)         | O(n)       |
| Deletion  | O(1)         | O(n)       |
| Search    | O(1)         | O(n)       |

> Worst case occurs when all keys hash to the same index (lots of collisions).

---

## ✅ **13. Summary**

| Feature       | Description                                      |
| ------------- | ------------------------------------------------ |
| Type          | Key–Value data structure                         |
| Principle     | Uses Hashing                                     |
| Performance   | Fast — O(1) average                              |
| Thread Safety | Synchronized                                     |
| Use Case      | When thread safety and fast lookups are required |
| Limitation    | No `null` keys/values                            |
