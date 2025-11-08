# How `ConcurrentHashMap` is built — detailed explanation + code examples

---

## 1) High-level goals & what changed in Java 8

- **Goals:** allow many concurrent **reads** without locking, allow many **independent updates** concurrently, keep operations fast (amortized O(1)), and resize without stopping the world. ([Oracle Docs][2])
- **Java 7 → Java 8 change:** segments (fixed array of locks) were removed. Java 8 uses a single table plus CAS (compare-and-swap) + per-bin fine-grained locking (synchronized on a bin’s head) and other coordination fields to get better scalability and simpler code. ([gee.cs.oswego.edu][3])

---

## 2) Core data structures (conceptual)

- `transient volatile Node<K,V>[] table;`
  The bucket array. Declared `volatile` so threads reliably see updates.

- `static final class Node<K,V> implements Map.Entry<K,V>`
  A node holds `final int hash; final K key; volatile V val; volatile Node<K,V> next;` for a linked-list bucket.

- `TreeNode` / `TreeBin`
  When a single bucket grows large (default threshold 8), it may be converted to a balanced tree (red-black) to bound lookup to O(log n). ([Stack Overflow][4])

- **Control fields** used for concurrency/resize:

  - `sizeCtl` — controls initialization and resize thresholds and also encodes resize state.
  - `transferIndex` — helps multiple threads split up work while migrating entries during resize.
  - Special marker nodes (forwarding nodes) are used in the old table to indicate a bin has been moved to the new table; readers encountering them go to the new table. ([Android Git Repositories][5])

---

## 3) How basic operations work (walk-through)

### `get(key)`

1. Compute hash (same mixing as HashMap) and read the volatile `table` reference.
2. Navigate to `tab[(tab.length - 1) & hash]`.
3. If head is `null` → return `null`.
4. If head matches key → return its value. Otherwise traverse the list or tree, comparing via `equals`.

- **Important:** `get` does **not acquire locks**; it reads volatile fields and follows `next` pointers. This makes reads lock-free (no `synchronized`), and extremely fast. Because updates are visible via volatile semantics/CAS, get returns a value consistent with concurrent updates in the sense required by `ConcurrentMap` (weakly consistent). ([Oracle Docs][2])

### `put(key, value)` — summary of `putVal` flow

- Fast path: if bucket is `null`, try to CAS a new node into that slot (no lock).
- If CAS fails (another thread inserted or bin non-empty), the thread either:

  - Synchronizes on the bin’s head node and traverses/updates the bin (list or tree), or
  - Helps with table transfer if the bin is a forwarding node (i.e., resize in progress).

- After insertion, counts are updated (using `addCount`), and if total size exceeds threshold, a resize is initiated — **and multiple threads can help perform the resize**. Resizing uses `transferIndex` to partition work among helpers. ([Android Git Repositories][5])

### Resizing (concurrent)

- When `size > threshold`, the first thread to notice starts a resize and allocates a new table (usually double size).
- Old-table bins are gradually transferred to the new table. Each transferred old bin is replaced by a **forwarding node** that points to the new table; other threads seeing that node help transfer or use the new table directly.
- The work is partitioned so multiple threads can do chunks of transfer concurrently — no single-thread stop-the-world rehash. ([Android Git Repositories][5])

---

## 4) Treeify (hot buckets)

- If the number of nodes in a single bucket reaches `TREEIFY_THRESHOLD` (8), and the table is large enough, that bucket is converted into a `TreeBin` (red-black tree).
- This reduces worst-case per-bucket traversal from O(n) to O(log n). ([Stack Overflow][4])

---

## 5) Iterator & atomic operations semantics

- Iterators are **weakly consistent**: they don’t throw `ConcurrentModificationException`; they reflect some snapshot of the map at or since creation. ([Oracle Docs][2])
- Atomic methods like `computeIfAbsent`, `compute`, `merge` are implemented carefully using CAS + retries + per-bin locking to ensure per-key atomicity. The user-supplied mapping functions may be retried if contention happens. ([DZone][6])

---

## 6) A **compact, educational** sketch of the idea (NOT the real source; simplified)

This tiny class is _only_ to illustrate the architecture (volatile table, Node, fast CAS insert, bin-level lock). It omits many details (treeification, forwarding nodes, resizing helpers, safe publication, memory barriers, performance optimizations). Do **not** use in production.

```java
// Educational sketch — simplified (not production)
class SimpleConcurrentMap<K,V> {
    static class Node<K,V> {
        final int hash; final K key;
        volatile V val;
        volatile Node<K,V> next;
        Node(int h, K k, V v, Node<K,V> n){ hash=h; key=k; val=v; next=n; }
    }

    // single volatile array reference
    private volatile Node<K,V>[] table;
    private final Object resizeLock = new Object(); // very simple global resize lock
    private final AtomicInteger size = new AtomicInteger();

    @SuppressWarnings("unchecked")
    SimpleConcurrentMap(int capacity){
        table = (Node<K,V>[])new Node[capacity];
    }

    public V get(Object key){
        int h = key.hashCode();
        Node<K,V>[] tab = table;
        int idx = (tab.length - 1) & h;
        Node<K,V> e = tab[idx];
        while(e != null){
            if(e.hash==h && (e.key==key || e.key.equals(key))) return e.val;
            e = e.next;
        }
        return null;
    }

    // Very naive put: try to CAS into empty slot, otherwise synchronize on bin head
    public V put(K key, V value){
        int h = key.hashCode();
        while(true){
            Node<K,V>[] tab = table;
            int idx = (tab.length - 1) & h;
            Node<K,V> head = tab[idx];
            if(head == null){
                Node<K,V> newNode = new Node<>(h, key, value, null);
                // simulated CAS using synchronized on table (real CHM uses Unsafe CAS)
                synchronized(this){
                    if(tab[idx] == null){
                        tab[idx] = newNode;
                        size.incrementAndGet();
                        return null;
                    } else continue;
                }
            } else {
                // simple bin-level locking (real CHM synchronizes on head)
                synchronized(head){
                    Node<K,V> e = head;
                    while(true){
                        if(e.hash==h && (e.key==key || e.key.equals(key))){
                            V old = e.val;
                            e.val = value;
                            return old;
                        }
                        if(e.next == null) break;
                        e = e.next;
                    }
                    e.next = new Node<>(h, key, value, null);
                    size.incrementAndGet();
                    return null;
                }
            }
        }
    }
}
```

What this sketch highlights:

- One `volatile` table reference (read by `get` without locks).
- Fast-path for "empty bin" insert (CAS in real code).
- Bin-level synchronization (`synchronized` on head in real code) for contended updates.

Real `ConcurrentHashMap` uses `Unsafe`/VarHandles CAS for slot installs and much more complex logic for safe, concurrent resize and memory visibility.

---

## 7) Real-world usage examples (demonstrations)

### Example A — many writers, many readers

```java
import java.util.concurrent.*;

public class CHMDemo {
    public static void main(String[] args) throws Exception {
        ConcurrentHashMap<String,Integer> map = new ConcurrentHashMap<>();
        ExecutorService ex = Executors.newFixedThreadPool(8);

        // 4 writers
        for (int w=0; w<4; w++) {
            final int id = w;
            ex.submit(() -> {
                for (int i=0; i<50_000; i++){
                    map.put("W"+id+"-"+i, i);
                }
            });
        }

        // 4 readers
        for (int r=0; r<4; r++) {
            ex.submit(() -> {
                long found=0;
                for (int i=0;i<200_000;i++){
                    Integer v = map.get("W0-" + (i % 50_000));
                    if (v!=null) found++;
                }
                System.out.println("Reader found: "+found);
            });
        }

        ex.shutdown();
        ex.awaitTermination(30, TimeUnit.SECONDS);
        System.out.println("Final size: " + map.size());
    }
}
```

- Demonstrates lock-free reads and concurrent puts. Use profiling to observe scaling.

### Example B — `computeIfAbsent` for safe initialization

```java
// Use a concurrent container as the value to avoid concurrent mutation issues.
ConcurrentHashMap<String, ConcurrentLinkedQueue<Integer>> map = new ConcurrentHashMap<>();

Runnable task = () -> {
    ConcurrentLinkedQueue<Integer> q =
        map.computeIfAbsent("key", k -> new ConcurrentLinkedQueue<>());
    q.add(1); // thread-safe addition to the queue
};
```

- `computeIfAbsent` ensures only one value is created per key even under contention; but the **value's own thread-safety** is the caller’s responsibility. ([DZone][6])

---

## 8) Important implementation details & caveats (practical)

- **No null keys/values:** `ConcurrentHashMap` does not permit `null` keys or `null` values (unlike `HashMap`).
- **Weakly consistent traversal:** Iterators may reflect some, all, or none of concurrent updates done after iterator creation.
- **Per-key atomic operations:** Methods like `putIfAbsent`, `compute`, `merge` provide atomicity for single-key updates (internally done with CAS loops and/or bin locking). ([Oracle Docs][2])
- **Resize helpers & forwarding nodes:** Resizing is cooperative: once resize starts, old bins become forwarding nodes that point to the new table; other threads encountering them will help move entries. This avoids a single-thread resize pause. ([Android Git Repositories][5])
- **Treeification threshold:** A bucket is treeified only if (a) its size exceeds a threshold (default 8) and (b) total table size is large enough — otherwise the map prefers to **expand the table** to reduce collisions. ([Stack Overflow][4])

---

## 9) Where to read the real source / references

- OpenJDK `ConcurrentHashMap` source (read the actual `putVal`, `transfer`, `TreeBin` methods) — the authoritative implementation. ([GitHub][1])
- Javadoc for `ConcurrentHashMap` (behavior and guarantees). ([Oracle Docs][2])
- Android / other trimmed sources (helpful comments explaining forwarding nodes). ([Android Git Repositories][5])

---

## 10) TL;DR (short summary)

- Java 8 `ConcurrentHashMap` uses a single volatile `table` array, CAS-based installs for empty slots, per-bin `synchronized` locking for contended updates, treeified bins for long lists, and cooperative concurrent resizing (forwarding nodes + helpers). Reads are lock-free; updates are fine-grained-locked or CASed — resulting in high concurrency and good performance for typical workloads. ([GitHub][1])

---

If you want I can:

- Walk through **actual `putVal` and `transfer`** code from OpenJDK and annotate line-by-line (I’ll quote small snippets and link to the exact source lines), **or**
- Produce a micro-benchmark comparing `ConcurrentHashMap` vs `Collections.synchronizedMap(new HashMap<>())` under different contention patterns and show results, **or**
- Explain one of the control fields in detail (e.g., how `sizeCtl` encodes different states and coordinates resizing threads).

Which follow-up would you prefer?

[1]: https://raw.githubusercontent.com/liachmodded/jdk/23999b66aef17423846af166abfd2296ff272733/src/java.base/share/classes/java/util/concurrent/ConcurrentHashMap.java?utm_source=chatgpt.com "https://raw.githubusercontent.com/liachmodded/jdk/..."
[2]: https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentHashMap.html?utm_source=chatgpt.com "ConcurrentHashMap (Java Platform SE 8 )"
[3]: https://gee.cs.oswego.edu/cgi-bin/viewcvs.cgi/jsr166/jsr166/src/main/java/util/concurrent/ConcurrentHashMap.java?revision=1.323&view=markup&utm_source=chatgpt.com "[ViewVC] Contents of: jsr166/jsr166/src/main/java/util/ ..."
[4]: https://stackoverflow.com/questions/24872732/concurrenthashmap-jdk-8-uses-treenodes-instead-of-list-why?utm_source=chatgpt.com "ConcurrentHashMap jdk 8 Uses TreeNodes instead of List ..."
[5]: https://android.googlesource.com/platform/prebuilts/fullsdk/sources/android-30/%2B/refs/heads/androidx-benchmark-release/java/util/concurrent/ConcurrentHashMap.java?utm_source=chatgpt.com "java/util/concurrent/ConcurrentHashMap.java"
[6]: https://dzone.com/articles/java-8-concurrenthashmap-atomic-updates?utm_source=chatgpt.com "Java 8: ConcurrentHashMap Atomic Updates"
