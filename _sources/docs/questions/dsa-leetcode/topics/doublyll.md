# Doubly Linked List

A **Doubly Linked List** is a data structure where:

- Each node contains **data**, a pointer to the **next** node, and a pointer to the **previous** node.
- It allows **bidirectional traversal** — both forward and backward.

---

## 💻 **Complete Implementation**

```java
public class DoublyLinkedList {

    Node head;
    Node tail;
    int length;

    // Node class for Doubly Linked List
    static class Node {
        int value;
        Node next;
        Node prev;

        Node(int value) {
            this.value = value;
            next = null;
            prev = null;
        }
    }

    DoublyLinkedList(int value) {
        Node newNode = new Node(value);
        head = newNode;
        tail = newNode;
        length = 1;
    }

    // 1
    public void append(int value) {
        Node newNode = new Node(value);

        if (length == 0) {
            head = newNode;
            tail = newNode;
        } else {
            tail.next = newNode;
            newNode.prev = tail;
            tail = newNode;
        }
        length++;
    }

    // 2
    public Node removeLast() {
        if (length == 0) {
            return null;
        }
        Node temp = tail;
        if (length == 1) {
            head = null;
            tail = null;
        } else {
            tail = tail.prev;
            tail.next = null;
            temp.prev = null;
        }
        length--;
        return temp;
    }

    // 3
    public void prepend(int value) {
        Node newNode = new Node(value);

        if (length == 0) {
            head = tail = newNode;
        } else {
            newNode.next = head;
            head.prev = newNode;
            head = newNode;
        }
        length++;
    }

    // 4
    public Node removeFirst() {
        if (length == 0) return null;

        Node temp = head;
        if (length == 1) {
            head = null;
            tail = null;
        } else {
            head = head.next;
            temp.next = null;
            head.prev = null;
        }
        length--;
        return temp;
    }

    // 5
    public Node get(int index) {
        if (index >= length || length < 0) return null;

        Node temp = head;
        if (index < length / 2) {
            for (int i = 0; i < index; i++) {
                temp = temp.next;
            }
        } else {
            temp = tail;
            for (int i = length - 1; i > index; i--) {
                temp = temp.prev;
            }
        }
        return temp;
    }

    // 6
    public boolean set(int index, int value) {
        if (index < 0 || index >= length) return false;

        Node temp = head;
        if (index < length / 2) {
            for (int i = 0; i < index; i++) {
                temp = temp.next;
            }
        } else {
            temp = tail;
            for (int i = length - 1; i > index; i--) {
                temp = temp.prev;
            }
        }
        temp.value = value;
        return true;
    }

    // 7
    public boolean insert(int index, int value) {
        if (index < 0 || index > length) return false;

        if (index == 0) {
            prepend(value);
            return true;
        }
        if (index == length) {
            append(value);
            return true;
        }
        Node newNode = new Node(value);

        Node prev = get(index - 1);
        Node after = prev.next;

        newNode.next = after;
        newNode.prev = prev;
        prev.next = newNode;
        after.prev = newNode;

        length++;
        return true;
    }

    // 8
    public Node remove(int index) {
        if(index < 0 || index > length) return null;

        if(index == 0) return removeFirst();

        if(index == length - 1) return removeLast();

        Node nodeToRemove = get(index);
        nodeToRemove.next.prev = nodeToRemove.prev;
        nodeToRemove.prev.next = nodeToRemove.next;
        nodeToRemove.next = null;
        nodeToRemove.prev = null;
        length--;
        return nodeToRemove;
    }

    // 9 for better readability
    public Node removeV2(int index) {
        if(index < 0 || index > length) return null;

        if(index == 0) return removeFirst();
        if(index == length - 1) return removeLast();

        Node nodeToRemove = get(index);

        Node before = nodeToRemove.prev;
        Node after = nodeToRemove.next;

        before.next = after;
        after.prev = before;
        nodeToRemove.next = null;
        nodeToRemove.prev = null;

        length--;
        return nodeToRemove;
    }

    public void printStats() {
        if (head != null) {
            System.out.println("Head Value: " + head.value);
        }
        if (tail != null) {
            System.out.println("Tail Value: " + tail.value);
        }
        System.out.println("Length: " + length);
        this.printList();
    }

    public void printList() {
        System.out.println("Doubly Linked List:");
        Node temp = head;
        System.out.print("Forward :         ");
        while (temp != null) {
            System.out.print(temp.value + " -> ");
            temp = temp.next;
        }
        System.out.print("null");
        System.out.println();
        temp = head;
        System.out.print("Backward: ");
        System.out.print("null");
        while (temp != null) {
            System.out.print(" <- " + temp.value);
            temp = temp.next;
        }
        System.out.println();
    }

}

```

---

## ⚙️ **Operations Implemented**

Here’s a detailed summary of all the **methods implemented** in your `DoublyLinkedList` class — along with a **description** and **time complexity analysis** 📘👇

---

### 🧾 **DoublyLinkedList Implementations Summary**

| **#** | **Method**                     | **Description**                                                                                                            |  **Time Complexity**  | **Space Complexity** |
| :---: | :----------------------------- | :------------------------------------------------------------------------------------------------------------------------- | :-------------------: | :------------------: |
| **1** | `append(int value)`            | Adds a new node with the given value **at the end** of the list. Updates both `tail` and `length`.                         |       **O(1)**        |       **O(1)**       |
| **2** | `removeLast()`                 | Removes the **last node** from the list and returns it. Handles empty and single-node cases properly.                      |       **O(1)**        |       **O(1)**       |
| **3** | `prepend(int value)`           | Inserts a new node with the given value **at the beginning** of the list. Updates both `head` and `length`.                |       **O(1)**        |       **O(1)**       |
| **4** | `removeFirst()`                | Removes the **first node** (head) of the list and returns it. Adjusts `head`, `tail`, and `length` accordingly.            |       **O(1)**        |       **O(1)**       |
| **5** | `get(int index)`               | Returns the **node at the specified index**. Traverses from head or tail depending on which is closer (optimizing lookup). | **O(n)** (worst case) |       **O(1)**       |
| **6** | `set(int index, int value)`    | Updates the **value** of the node at the specified index. Uses `get()` internally to locate the node.                      |       **O(n)**        |       **O(1)**       |
| **7** | `insert(int index, int value)` | Inserts a new node **at a given index**. Handles inserting at the beginning, middle, or end.                               |       **O(n)**        |       **O(1)**       |
| **8** | `remove(int index)`            | Removes the node **at a specific index** and returns it. Adjusts surrounding nodes’ pointers.                              |       **O(n)**        |       **O(1)**       |

## Interview / LeetCode Questions

### DLL Palindrome Checker

**Problem Statement:**

- Write a method to determine whether a given doubly linked list reads the same forwards and backwards.
- For example, if the list contains the values [1, 2, 3, 2, 1], then the method should return true, since the list is a palindrome.
- If the list contains the values [1, 2, 3, 4, 5], then the method should return false, since the listis not a palindrome.

Method name: `isPalindrome`
Return Type: `boolean`

**Code Implementaion**

```java
    public boolean isPalindrome() {
        // when no values i.e length is ZERO
        if(head == null || tail == null) {
            return false;
        }
        // when length is ONE
        if(head == tail) {
            return true;
        }
        // when length is TWO or MORE
        Node forwardNode = head;
        Node backwordNode = tail;
        for (int i = 0; i < length / 2; i++) {
            if(forwardNode.value != backwordNode.value) {
                return false;
            }
            forwardNode = forwardNode.next;
            backwordNode = backwordNode.prev;
        }
        return true;
    }
```

### DLL Reverse

**Problem Statement:**
Implement a method called reverse() that reverses the order of the nodes in the list.
This method should reverse the order of the nodes in the list by manipulating the pointers of each node, not by swapping the values within the nodes.

**Method Signature:**

```java
public void reverse()
```

**Output:**
No explicit output is returned. However, the method should modify the doubly linked list such that the order of the nodes is reversed.

**Constraints:**
The doubly linked list may be empty or have one or more nodes.

**Example:**
Consider the following doubly linked list:

- Head: 1, Tail: 5, Length: 5
- Doubly Linked List: 1 <-> 2 <-> 3 <-> 4 <-> 5

After calling reverse(), the list should be:

- Head: 5, Tail: 1, Length: 5
- Doubly Linked List: 5 <-> 4 <-> 3 <-> 2 <-> 1

**Code Implementation:**

```java
    public void reverse() {
        Node current = head;
        Node temp = null;

        while (current != null) {
            temp = current.prev;
            current.prev = current.next;
            current.next = temp;
            current = current.prev;
        }

        temp = head;
        head = tail;
        tail = temp;
    }
```

### DLL Partitioned

**Problem Statement:**

Write a method called partitionList that rearranges the nodes in a doubly linked list so that all nodes with values less than a given number x come before nodes greater than or equal to x.

This must be done by relinking the existing nodes (not by creating new ones). The method should update both .next and .prev pointers correctly.

The relative order of nodes within each partition must be preserved.

**📌 Example Inputs and Outputs:**

Input: 3 <-> 8 <-> 5 <-> 10 <-> 2 <-> 1, x = 5
Output: 3 <-> 2 <-> 1 <-> 8 <-> 5 <-> 10

Input: 1 <-> 2 <-> 3, x = 5
Output: 1 <-> 2 <-> 3

Input: 6 <-> 7 <-> 8, x = 5
Output: 6 <-> 7 <-> 8

```java
    // Method to partition the list
    public void partitionList(int x) {

        if (head == null || head.next == null) return;

        Node lessHead = null, lessTail = null;
        Node greaterHead = null, greaterTail = null;
        Node current = head;

        while (current != null) {
            Node nextNode = current.next; // save next node
            current.next = null;
            current.prev = null;

            if (current.value < x) {
                // append to less list
                if (lessHead == null) {
                    lessHead = lessTail = current;
                } else {
                    lessTail.next = current;
                    current.prev = lessTail;
                    lessTail = current;
                }
            } else {
                // append to greater or equal list
                if (greaterHead == null) {
                    greaterHead = greaterTail = current;
                } else {
                    greaterTail.next = current;
                    current.prev = greaterTail;
                    greaterTail = current;
                }
            }
            current = nextNode;
        }

        // Combine the two lists
        if (lessTail != null) {
            head = lessHead;
            lessTail.next = greaterHead;
            if (greaterHead != null) greaterHead.prev = lessTail;
        } else { // less list is empty
            head = greaterHead;
        }
    }
```

### DLL Reverse Between `TODO`

### DLL Swap Nodes in Pairs `TODO`
