# Stack

## **1. What is a Stack?**

A **stack** is a **linear data structure** that follows **LIFO (Last In, First Out)** principle.

- The **last element added** to the stack is the **first one to be removed**.

Think of a stack of plates:

- You **put a plate on top** → push.
- You **take the top plate** → pop.

---

## **2. Stack Operations**

| Operation   | Description                                                 |
| ----------- | ----------------------------------------------------------- |
| `push()`    | Adds an element to the top of the stack                     |
| `pop()`     | Removes and returns the top element                         |
| `peek()`    | Returns the top element without removing it                 |
| `isEmpty()` | Checks if the stack is empty                                |
| `search()`  | Returns 1-based position of element from the top (optional) |

---

## **3. Stack in Java**

Java provides a built-in **`Stack` class** in `java.util` package.

```java
import java.util.Stack;

public class StackExample {
    public static void main(String[] args) {
        Stack<Integer> stack = new Stack<>();

        // Push elements
        stack.push(10);
        stack.push(20);
        stack.push(30);
        System.out.println("Stack after pushes: " + stack);

        // Peek top element
        System.out.println("Top element: " + stack.peek());

        // Pop elements
        System.out.println("Popped element: " + stack.pop());
        System.out.println("Stack after pop: " + stack);

        // Check if stack is empty
        System.out.println("Is stack empty? " + stack.isEmpty());

        // Search element
        System.out.println("Position of 10: " + stack.search(10)); // 1-based position from top
    }
}
```

**Output:**

```
Stack after pushes: [10, 20, 30]
Top element: 30
Popped element: 30
Stack after pop: [10, 20]
Is stack empty? false
Position of 10: 2
```

---

## **4. Stack Using `Deque` (Recommended for Modern Java)**

Java’s `Stack` class is considered legacy. A modern approach is using **`Deque` interface** with `ArrayDeque`:

```java
import java.util.ArrayDeque;
import java.util.Deque;

public class StackWithDeque {
    public static void main(String[] args) {
        Deque<Integer> stack = new ArrayDeque<>();

        // Push elements
        stack.push(100);
        stack.push(200);
        stack.push(300);

        System.out.println("Stack: " + stack);

        // Peek top element
        System.out.println("Top element: " + stack.peek());

        // Pop elements
        System.out.println("Popped: " + stack.pop());
        System.out.println("Stack after pop: " + stack);

        // Check empty
        System.out.println("Is empty? " + stack.isEmpty());
    }
}
```

✅ `ArrayDeque` is **faster and more efficient** than `Stack`.

---

## **5. Real-Time Use Cases of Stack**

1. **Undo/Redo operations in editors**
2. **Browser back/forward history**
3. **Expression evaluation and parsing**
4. **Call stack in program execution**
5. **Backtracking problems** (maze, puzzles, DFS traversal)

---

## **6. Custom Stack Implementation**

You can implement a stack manually using an **array** or **linked list**:

### Using Array:

```java
class MyStack {
    private int[] arr;
    private int top;
    private int capacity;

    public MyStack(int size) {
        arr = new int[size];
        capacity = size;
        top = -1;
    }

    public void push(int x) {
        if (top == capacity - 1) {
            System.out.println("Stack Overflow");
            return;
        }
        arr[++top] = x;
    }

    public int pop() {
        if (top == -1) {
            System.out.println("Stack Underflow");
            return -1;
        }
        return arr[top--];
    }

    public int peek() {
        if (top == -1) return -1;
        return arr[top];
    }

    public boolean isEmpty() {
        return top == -1;
    }
}

public class TestCustomStack {
    public static void main(String[] args) {
        MyStack stack = new MyStack(5);
        stack.push(1);
        stack.push(2);
        stack.push(3);

        System.out.println("Top element: " + stack.peek());
        System.out.println("Popped: " + stack.pop());
    }
}
```

---

### Using Linked List:

```{code-block} java
:caption: CustomStack Class

public class CustomStack {

    private Node top;
    private int height;

    class Node {
        int value;
        Node next;

        Node(int value) {
            this.value = value;
        }
    }

    CustomStack(int value) {
        Node newNode = new Node(value);
        top = newNode;
        this.height = 1;
    }

    // 1
    public void push(int value) {
        Node newNode = new Node(value);

        if(height == 0) {
            top = newNode;
        } else {
            newNode.next = top;
            top = newNode;
        }
        height++;
    }

    // 2
    public Node pop() {
        if(height == 0) {
            return null;
        }
        Node popped = top;

        if(height == 1) {
            top = null;
        } else {
            top = top.next;
            popped.next = null;
        }
        height--;
        return popped;
    }

    public void printStats() {
        if(top != null) {
            System.out.println("Top: " + top.value);
        }
        System.out.println("Height: " + height);
        printStack();
    }

    public void printStack() {
        Node current = top;
        while(current != null) {
            System.out.println(current.value);
            System.out.println("⬇️");
            current = current.next;
        }
    }

}

```

```{code-block} java
:caption: Main Class
public class App
{
    public static void main( String[] args )
    {

        CustomStack stack = new CustomStack(10);

        stack.push(20);
        stack.push(30);
        stack.push(40);
        stack.push(50);
        System.out.println(stack.pop().value);
        stack.printStats();

    }
}

```

---

## **7. Comparison of Stack vs Deque in Java**

**Key point**: ArrayDeque is recommended instead of Stack in almost all cases because it’s faster, simpler, and more versatile.

Recommendation

- For new Java code, always prefer ArrayDeque over Stack.
- Only use Stack if you need legacy code compatibility.

| Feature       | `Stack`                                     | `Deque` (`ArrayDeque`)                                   |
| ------------- | ------------------------------------------- | -------------------------------------------------------- |
| Type          | Class                                       | Interface (`ArrayDeque` implements `Deque`)              |
| Package       | `java.util.Stack`                           | `java.util`                                              |
| Principle     | LIFO (Last-In-First-Out)                    | Can be used as LIFO (Stack) or FIFO (Queue)              |
| Legacy Status | Considered **legacy**, synchronized, slower | Modern, unsynchronized, more efficient                   |
| Thread Safety | Synchronized methods (thread-safe)          | Not thread-safe by default                               |
| Recommended   | Rarely; legacy                              | Preferable for stack/queue implementation in modern Java |

## **8. Summary**

- **Stack** → LIFO data structure.
- **Built-in Java class** → `java.util.Stack`.
- **Modern alternative** → `ArrayDeque`.
- **Operations** → push, pop, peek, isEmpty, search.
- **Real-world use cases** → undo/redo, browser history, parsing expressions, DFS, function calls.
