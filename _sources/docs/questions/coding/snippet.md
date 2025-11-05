# Analyze the Output

##### Try-Catch-Finally Return

::::{tab-set}
:::{tab-item} Code

```java
public class TryCatch {
    public static void main(String[] args) {
        System.out.println(test());
    }

    static int test() {
        try {
            System.out.println("Inside try block");
            return 1;
        } catch(Exception e) {
            return 2;
        }finally {
            return 3;
        }
    }
}

```

:::
:::{tab-item} Output

```{code-block} text
Inside try block
3
```

:::
::::

:::{note}
Even when the try or catch block returns a value, the finally block (if it also returns something) will override and replace that return value.
:::

##### Fail-Fast Operations

::::{tab-set}
:::{tab-item} Code

```java
ArrayList<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");

Iterator<String> it = list.iterator();
while(it.hasNext()) {
    String val = it.next();
    System.out.println(val);
    // Structural modification during iteration
    list.add("D"); // This will cause ConcurrentModificationException
}
```

:::
:::{tab-item} Output

```{code-block} text
A
Exception in thread "main" java.util.ConcurrentModificationException
```

:::
::::

:::{note}
**Definition:**
A Fail-Fast iterator immediately throws a ConcurrentModificationException if the underlying collection is structurally modified while iterating (except through the iterator’s own remove or add methods if supported).

**Key points for ArrayList:**

- ArrayList uses fail-fast iterators.
- Structural modification means adding, removing, or changing the size of the list (not just changing an element’s value).
- The iterator keeps a modCount variable internally. If modCount changes during iteration, it throws ConcurrentModificationException.
  :::

##### Fail-Safe Operations

::::{tab-set}
:::{tab-item} Code

```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
list.add("A");
list.add("B");
list.add("C");

Iterator<String> it = list.iterator();
while(it.hasNext()) {
    String val = it.next();
    System.out.println(val);
    list.add("D"); // No exception here
}

System.out.println("Final List: " + list);

```

:::
:::{tab-item} Output

```{code-block} text
// Notice that the iterator does not see the newly added elements.
A
B
C
Final List: [A, B, C, D, D, D]
```

:::
::::

:::{note}
**Definition:**
A Fail-Safe iterator does not throw ConcurrentModificationException if the collection is modified during iteration. It works on a copy of the data, not the original collection.

**Key points:**

- Fail-Safe iterators are found in collections like CopyOnWriteArrayList, ConcurrentHashMap, etc.
- They iterate over a snapshot of the collection, so modifications in the original collection do not affect the iteration.
  :::

##### Modifying a list during an index-based loop

::::{tab-set}
:::{tab-item} Code

```java
List<Integer> list = new ArrayList<>();
list.add(1);

for (int i = 0; i < list.size(); i++) {
    list.add(2);
    System.out.println(list.get(i));
}

```

:::
:::{tab-item} Output

```{code-block} text
1
2
2
2
2
... Continues infinitely
```

:::
::::

:::{note}

- ArrayList fail-fast only applies to iterators.
- Modifying a list during an index-based loop can cause unexpected behavior like infinite loops.
- Safe alternatives:
  - Use iterator with remove/add methods properly.
  - Use a separate collection to store additions and merge later.
    :::
