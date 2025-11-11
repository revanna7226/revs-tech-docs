# Queue

## **1. What is a Queue?**

A **Queue** is a **linear data structure** that follows the **FIFO (First In, First Out)** principle:

- The **first element added** is the **first one removed**.
- Think of it like a line at a ticket counter.

### Key Points:

- Elements are added at the **rear** (enqueue).
- Elements are removed from the **front** (dequeue).
- Used in **task scheduling**, **order processing**, **printer queues**, etc.

---

## **2. Queue in Java**

In Java, `Queue` is an **interface** in `java.util` package.

### Common Implementing Classes:

| Class           | Notes                                                     |
| --------------- | --------------------------------------------------------- |
| `LinkedList`    | Implements `Queue` and `Deque` interfaces; allows nulls.  |
| `PriorityQueue` | Orders elements based on priority.                        |
| `ArrayDeque`    | Resizable array implementation; faster than `LinkedList`. |

### Queue Interface Methods

| Method       | Description                                               |
| ------------ | --------------------------------------------------------- |
| `add(E e)`   | Adds element to the queue; throws exception if full.      |
| `offer(E e)` | Adds element to the queue; returns `false` if full.       |
| `remove()`   | Removes and returns head; throws exception if empty.      |
| `poll()`     | Removes and returns head; returns `null` if empty.        |
| `element()`  | Returns head without removing; throws exception if empty. |
| `peek()`     | Returns head without removing; returns `null` if empty.   |

---

### **Example 1: Queue using LinkedList**

```java
import java.util.LinkedList;
import java.util.Queue;

public class QueueExample {
    public static void main(String[] args) {
        Queue<Integer> queue = new LinkedList<>();

        // Enqueue elements
        queue.add(10);
        queue.add(20);
        queue.add(30);
        queue.offer(40); // alternative to add

        System.out.println("Queue: " + queue);

        // Peek at the front element
        System.out.println("Front element: " + queue.peek());

        // Dequeue elements
        int removed = queue.remove();
        System.out.println("Removed: " + removed);

        System.out.println("Queue after removal: " + queue);

        // Poll example (does not throw exception)
        int polled = queue.poll();
        System.out.println("Polled: " + polled);
        System.out.println("Queue now: " + queue);
    }
}
```

**Output:**

```
Queue: [10, 20, 30, 40]
Front element: 10
Removed: 10
Queue after removal: [20, 30, 40]
Polled: 20
Queue now: [30, 40]
```

---

### **Example 2: Queue using PriorityQueue**

```java
import java.util.PriorityQueue;
import java.util.Queue;

public class PriorityQueueExample {
    public static void main(String[] args) {
        Queue<Integer> pq = new PriorityQueue<>();

        pq.add(30);
        pq.add(10);
        pq.add(20);

        System.out.println("PriorityQueue: " + pq);

        // Poll removes the smallest element
        System.out.println("Removed: " + pq.poll());
        System.out.println("Queue now: " + pq);
    }
}
```

**Output (smallest element removed first):**

```
PriorityQueue: [10, 30, 20]
Removed: 10
Queue now: [20, 30]
```

---

### **Example 3: Queue using ArrayDeque**

```java
import java.util.ArrayDeque;
import java.util.Queue;

public class ArrayDequeQueueExample {
    public static void main(String[] args) {
        Queue<String> queue = new ArrayDeque<>();

        queue.add("A");
        queue.add("B");
        queue.add("C");

        System.out.println("Queue: " + queue);

        queue.remove();
        System.out.println("Queue after removal: " + queue);

        System.out.println("Front element: " + queue.peek());
    }
}
```

**Output:**

```
Queue: [A, B, C]
Queue after removal: [B, C]
Front element: B
```

---

## **3. Custom Queue Implementation**

You can implement a queue manually using an **array** or **linked list**:

### Using Array:

```java

```

### Using Linked List:

````{code-block} java
:caption: Custom Queue Implementation using Linked List

package com.revs;

public class CustomQueue {
    private Node first;
    private Node last;
    private int length;

    class Node {
        int value;
        Node next;
        Node(int value) {
            this.value = value;
        }
    }

    CustomQueue(int value) {
        Node newNode = new Node(value);
        first = newNode;
        last = newNode;
        length = 1;
    }

    // 1
    public void enqueue(int value) {
        Node newNode = new Node(value);
        if(length == 0) {
            first = newNode;
            last = newNode;
        } else {
            last.next = newNode;
            last = newNode;
        }
        length++;
    }

    // 2
    public Node dequeue() {
        if(length == 0) return null;

        Node temp = first;
        if(length == 1) {
            first = null;
            last = null;
        } else {
            first = first.next;
            temp.next = null;
        }
        length--;
        return temp;
    }

    // stats
    public void printStats() {
        if(first != null) {
            System.out.println("First: " + first.value);
        }
        if(last != null) {
            System.out.println("Last: " + last.value);
        }
        System.out.println("Length: " + length);

        printQueue();
    }

    private void printQueue() {
        Node current = first;
        while(current != null) {
            System.out.print(current.value + " -> ");
            current = current.next;
        }
    }

}

```{code-block} java
:caption: Main Class

public class App
{
    public static void main( String[] args )
    {
        CustomQueue q = new CustomQueue(10);
        q.enqueue(20);
        q.enqueue(30);
        q.enqueue(40);
        q.printStats();
        System.out.println();
        System.out.println(q.dequeue().value);;
        q.printStats();
    }
}

````

| Method               | Description                                              |
| -------------------- | -------------------------------------------------------- |
| `enqueue(int value)` | which adds a new node to the end of the queue.           |
| `dequeue()`          | which removes and returns the first node from the queue. |

---

## **4. Key Points to Remember**

1. **FIFO principle** – first in, first out.
2. **Interface vs Implementation** – `Queue` is an interface, choose an implementing class.
3. **Methods behavior** – `add/remove` throw exceptions, `offer/poll/peek` return null or false safely.
4. **Specialized queues**:

   - `PriorityQueue` – element order based on priority.
   - `ArrayDeque` – fast, resizable queue.
