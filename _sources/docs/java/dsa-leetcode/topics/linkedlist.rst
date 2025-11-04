Linked List
+++++++++++


Introduction
============

A **Linked List** is a **linear data structure** in which elements are stored in **nodes**.  

Each node contains:
    - **Data** — the actual value.
    - **Next** — a reference (pointer) to the next node in the sequence.

Unlike arrays, linked lists are **not stored in contiguous memory**, making insertion and deletion more efficient but access slower.

Advantages of Linked List
=========================

- Dynamic size (no need to define capacity upfront).
- Easy insertion and deletion operations.
- Efficient memory utilization (no memory wastage).

Disadvantages of Linked List
============================

- Random access is not possible (must traverse sequentially).
- Uses extra memory for storing references.
- Reverse traversal is difficult in singly linked lists.


Types of Linked Lists
=====================

1. **Singly Linked List**
   - Each node points to the next node.
   - Traversal is one-directional.

2. **Doubly Linked List**
   - Each node has references to both previous and next nodes.
   - Traversal is bi-directional.

3. **Circular Linked List**
   - The last node points back to the first node.

Structure of a Linked List Node
===============================

.. code-block:: java

   class Node {
       int value;
       Node next;

       Node(int value) {
           this.value = value;
           this.next = null;
       }
   }

Each ``Node`` object stores an integer value and a pointer (reference) to the next node.

Singly Linked List Implementation
=================================
.. code-block:: java

    package com.revs;

    public class SinglyLinkedList {
        private Node head;
        private Node tail;
        private int length;

        class Node {
            int value;
            Node next;

            Node(int value) {
                this.value = value;
                this.next = null;
            }
        }

        public SinglyLinkedList(int value) {
            Node newNode = new Node(value);
            head = newNode;
            tail = newNode;
            length = 1;
        }

        public void printList() {
            Node temp = head;
            while (temp != null) {
                System.out.println(temp.value);
                temp = temp.next;
            }
        }

        public void getHead() {
            if (head == null) {
                System.out.println("Head: null");
            } else {
                System.out.println("Head: " + head.value);
            }
        }

        public void getTail() {
            if (head == null) {
                System.out.println("Tail: null");
            } else {
                System.out.println("Tail: " + tail.value);
            }
        }

        public void getLength() {
            System.out.println("Length: " + length);
        }

        public void append(int value) {
            Node newNode = new Node(value);
            if (length == 0) {
                head = newNode;
                tail = newNode;
            } else {
                tail.next = newNode;
                tail = newNode;
            }
            length++;
        }

        public Node removeLast() {
            if (length == 0) return null;
            Node temp = head;
            Node pre = head;
            while(temp.next != null) {
                pre = temp;
                temp = temp.next;
            }
            tail = pre;
            tail.next = null;
            length--;
            if (length == 0) {
                head = null;
                tail = null;
            }
            return temp;
        }

        public void prepend(int value) {
            Node newNode = new Node(value);
            if (length == 0) {
                head = newNode;
                tail = newNode;
            } else {
                newNode.next = head;
                head = newNode;
            }
            length++;
        }

        public Node removeFirst() {
            if (length == 0) return null;
            Node temp = head;
            head = head.next;
            temp.next = null;
            length--;
            if (length == 0) {
                tail = null;
            }
            return temp;
        }

        public Node get(int index) {
            if (index < 0 || index >= length) return null;
            Node temp = head;
            for(int i = 0; i < index; i++) {
                temp = temp.next;
            }
            return temp;
        }

        public boolean set(int index, int value) {
            Node temp = get(index);
            if (temp != null) {
                temp.value = value;
                return true;
            }
            return false;
        }

        public boolean insert(int index, int value)  {
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
            Node temp = get(index - 1);
            newNode.next = temp.next;
            temp.next = newNode;
            length++;
            return true;
        }

        public Node remove(int index) {
            if (index < 0 || index >= length) return null;
            if (index == 0) return removeFirst();
            if (index == length - 1) return removeLast();

            Node prev = get(index - 1);
            Node temp = prev.next;

            prev.next = temp.next;
            temp.next = null;
            length--;
            return temp;
        }

        public void reverse() {
            Node temp = head;
            head = tail;
            tail = temp;
            Node after = temp.next;
            Node before = null;
            for (int i = 0; i < length; i++) {
                after = temp.next;
                temp.next = before;
                before = temp;
                temp = after;
            }
        }

    }

Interview/LeetCode Questions
============================

Find Middle Node
****************

Problem Statement:
    - Given the head of a **singly linked list**, return the **middle node** of the linked list.
    - If there are two middle nodes, return the **second middle node**.

**Example:**

.. code-block:: text

    Input: 1 -> 2 -> 3 -> 4 -> 5 -> null
    Output: 3

    Input: 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> null
    Output: 4

Approach 1: Using Slow and Fast Pointers
-----------------------------------------

This is the **optimal and most common approach** used in LeetCode problems.

**Idea:**
   - Use two pointers: ``slow`` and ``fast``.
   - ``slow`` moves one step at a time.
   - ``fast`` moves two steps at a time.
   - When ``fast`` reaches the end, ``slow`` will be at the **middle**.

.. code-block:: java

    public Node findMiddleNode() {
	    Node slow = head;
	    Node fast = head;
	    
	    while(fast != null && fast.next != null) {
	        slow = slow.next;
	        fast = fast.next.next;
	    }
	    
	    return slow;
	}

Approach 2: Using Length Count (Less Efficient)
------------------------------------------------

Another approach is:
   1. Traverse the list once to count the number of nodes.
   2. Traverse again up to ``length / 2`` and return that node.   

.. code-block:: java

	public Node findMiddleNode() {
	    Node pointerNode = head;
	    int length = 0;
	    
	    while(pointerNode != null) {
	        pointerNode = pointerNode.next;
	        length++;
	    }
	    
	    int midIndex = length / 2;
	    pointerNode = head;
	    for(int i = 0; i < midIndex; i++) {
	        pointerNode = pointerNode.next;
	    }
	    
	    
	    return pointerNode;
	}

Detect Loop in Linked List (Floyd's Algorithm)
**********************************************

Problem Statement:
    - Given the head of a **singly linked list**, determine whether the linked list contains a **cycle (loop)**.
    - A loop exists if any node's ``next`` pointer points to a previously visited node.

Example:

.. code-block:: text

   Input: 1 -> 2 -> 3 -> 4 -> 5 -> 3 (loop back to node 3)
   Output: true

   Input: 1 -> 2 -> 3 -> 4 -> 5 -> null
   Output: false

Floyd's Cycle Detection Algorithm
-----------------------------------

Also known as the **Tortoise and Hare Algorithm**.

**Idea:**
   - Use two pointers:
       - **slow** (tortoise): moves **1 step** at a time.
       - **fast** (hare): moves **2 steps** at a time.
   - If there is a **loop**, both pointers will eventually **meet** inside the loop.
   - If ``fast`` or ``fast.next`` becomes ``null``, then there is **no loop**.    

.. code-block:: java

    public boolean hasLoop() {
	    Node slow = head;
	    Node fast = head;
	    
	    while(fast != null && fast.next != null) {
	        slow = slow.next;
	        fast = fast.next.next;
	        
	        if(fast == slow) {
	            return true;
	        }
	    }
	    return false;
	}


Find Kth Node from End in Singly Linked List
********************************************

Problem Statement:
    Given the head of a **singly linked list**, return the **Kth node from the end** of the list.

**Example:**

.. code-block:: text

   Input: Linked List = 1 -> 2 -> 3 -> 4 -> 5,  K = 2
   Output: Node with value 4

   Explanation:
   2nd node from the end is node 4.

Approach 1: Two-Pointer Technique (Efficient)
---------------------------------------------

We use two pointers, often called **fast** and **slow**.

Idea:
   1. Move the **fast** pointer `k` steps ahead first.
   2. Then move **slow** and **fast** together **until fast reaches the end**.
   3. The **slow** pointer now points to the **Kth node from the end**.


Algorithm Steps:
   1. Initialize two pointers: ``slow = head`` and ``fast = head``.
   2. Move ``fast`` pointer **k steps ahead**.
        - If ``fast`` becomes ``null`` before completing k steps, return ``null`` (list shorter than k).
   3. Move both ``slow`` and ``fast`` **one step at a time** until ``fast`` reaches ``null``.
   4. When ``fast == null``, ``slow`` points to the **Kth node from the end**.

.. code-block:: java

    public static Node findKthFromEnd(Node head, int k) {
        if (head == null || k <= 0) return null;

        Node slow = head;
        Node fast = head;

        // Move fast k steps ahead
        for (int i = 0; i < k; i++) {
            if (fast == null) return null; // k > length of list
            fast = fast.next;
        }

        // Move both one step at a time
        while (fast != null) {
            slow = slow.next;
            fast = fast.next;
        }

        // slow is now the Kth node from the end
        return slow;
    }

Approach 2: Using Length (Simpler, Less Efficient)
--------------------------------------------------

1. Traverse the list to find its total length ``n``.
2. The Kth node from the end is the ``(n - k + 1)th`` node from the beginning.
3. Traverse again to that node.

**Time Complexity:** ``O(2n)``  
**Space Complexity:** ``O(1)``    

