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

**Code Implementation:**

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

**Code Implementation:**

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

**Code Implementation:**

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

**Code Implementation:**

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

Remove Duplicates
********************************************

You are given a singly linked list that contains integer values, where some of these values may be duplicated.

Note: this linked list class does NOT have a tail which will make this method easier to implement.

Steps:
    - Your task is to implement a method called ``removeDuplicates()`` within the LinkedList class that removes all duplicate values from the list.
    - Your method should not create a new list, but rather modify the existing list in-place, preserving the relative order of the nodes.
    - You can implement the ``removeDuplicates()`` method in two different ways:
        - Using a Set (HashSet) - This approach will have a time complexity of O(n), where n is the number of nodes in the linked list. You are allowed to use the provided Set data structure in your implementation.
        - Without using a Set - This approach will have a time complexity of O(n^2), where n is the number of nodes in the linked list. You are not allowed to use any additional data structures for this implementation.

Here is the method signature you need to implement:

.. code-block:: java

    public void removeDuplicates() {
        // Your implementation goes here
    }

Example:
    - **Input:** LinkedList: 1 -> 2 -> 3 -> 1 -> 4 -> 2 -> 5
    - **Output:** LinkedList: 1 -> 2 -> 3 -> 4 -> 5

✅ 1. Using a Set (O(n) time, O(n) space)
------------------------------------------
Explanation
    - We traverse the list once, maintaining a HashSet<Integer> to track which values we’ve seen.
    - If a node’s value is already in the set, we remove it by adjusting the previous node’s next pointer.

**Code Implementation:**

.. code-block:: java

    public void removeDuplicates() {
        if (head == null) return;

        Set<Integer> seen = new HashSet<>();
        Node current = head;
        Node prev = null;

        while (current != null) {
            if (seen.contains(current.data)) {
                // Duplicate found → skip current node
                prev.next = current.next;
            } else {
                seen.add(current.data);
                prev = current;
            }
            current = current.next;
        }
    }    

🚫 2. Without using a Set (O(n²) time, O(1) space)
---------------------------------------------------

Explanation:
    We use two pointers:
        - current traverses each node,
        - runner checks all subsequent nodes for duplicates and removes them inline.    

**Code Implementation:**

.. code-block:: java

    public void removeDuplicates() {
        Node current = head;

        while (current != null) {
            Node runner = current;
            while (runner.next != null) {
                if (runner.next.data == current.data) {
                    // Remove duplicate
                    runner.next = runner.next.next;
                } else {
                    runner = runner.next;
                }
            }
            current = current.next;
        }
    }

Binary to Decimal
********************************************

Problem Statement:
    You have a linked list where each node represents a binary digit (0 or 1). The goal of the binaryToDecimal function is to convert this binary number, represented by the linked list, into its decimal equivalent.

    .. code-block:: java

        // Function Signature:
        public int binaryToDecimal()

How Binary to Decimal Conversion Works:
    In binary-to-decimal conversion, each position of a binary number corresponds to a specific power of 2, starting from the rightmost digit.

    - The rightmost digit is multiplied by 2^0 (which equals 1).
    - The next digit to the left is multiplied by 2^1 (which equals 2).
    - The digit after that is multiplied by 2^2 (which equals 4). ... and so on.    

To find the decimal representation:
    - Multiply each binary digit by its corresponding power of 2 value.
    - Sum up all these products.

Example Execution with Binary 101:
    - Start with num = 0.
    - Process 1 (from the head of the linked list): num = 0 * 2 + 1 = 1
    - Process 0: num = 1 * 2 + 0 = 2
    - Process 1: num = 2 * 2 + 1 = 5
    - Return num, which is 5.

Steps Involved in the Function:
    - A variable num is initialized to 0, which will store our computed decimal number.
    - Starting from the head of the linked list (the leftmost binary digit), iterate through each node until the end.
    - For every node, double the current value of num (this is analogous to shifting in binary representation). Then, add the binary digit of the current node.
    - Move to the next node and repeat until you've visited all nodes.
    - Return the value in num, which now represents the decimal value of the binary number in the linked list.

**Code Implementation:**

.. code-block:: java

    public int binaryToDecimal() {
        int decimal = 0;
        Node current = head;
        
        while(current != null) {
            decimal = decimal * 2 + current.value;
            current = current.next;
        }
        return decimal;
    } 

Partion List
******************

Problem Statement:
    Given a value x you need to rearrange the linked list such that all nodes with a value less than x come before all nodes with a value greater than or equal to x.
    Additionally, the relative order of nodes in both partitions should remain unchanged.

Constraints:
    - The solution should traverse the linked list at most once.
    - The solution should not create a new linked list.

**Approach:**
We'll use two dummy pointers to rearrange the nodes in place:
    - ``beforeStart`` and ``beforeEnd`` → track the “less than x” sublist
    - ``afterStart`` and ``afterEnd`` → track the “greater than or equal to x” sublist

As we traverse the list once, we'll:
    - Detach each node from the main list.
    - Append it to the correct partition (before or after).
    - After traversal, connect both partitions.

No new nodes are created — only pointers are rearranged.

**Code Implementation:**

.. code-block:: java

    public void partitionList(int x) {
        if (head == null) return;

        Node beforeStart = null, beforeEnd = null;
        Node afterStart = null, afterEnd = null;
        Node current = head;

        while (current != null) {
            Node nextNode = current.next; // Save next
            current.next = null;          // Detach current

            if (current.value < x) {
                // Add to before list
                if (beforeStart == null) {
                    beforeStart = current;
                    beforeEnd = beforeStart;
                } else {
                    beforeEnd.next = current;
                    beforeEnd = current;
                }
            } else {
                // Add to after list
                if (afterStart == null) {
                    afterStart = current;
                    afterEnd = afterStart;
                } else {
                    afterEnd.next = current;
                    afterEnd = current;
                }
            }

            current = nextNode;
        }

        // If there are no elements less than x
        if (beforeStart == null) {
            head = afterStart;
            return;
        }

        // Merge before list and after list
        beforeEnd.next = afterStart;
        head = beforeStart;
    }      


Reverse Between
********************************************

Problem Statement:
    Given the head of a singly linked list and two integers left and right
    where left <= right, reverse the nodes of the list from position left 
    to position right, and return the reversed list.

Example: 
    - **Input:** LinkedList: 1 -> 2 -> 3 -> 4 -> 5, left = 2, right = 4
    - **Output:** LinkedList: 1 -> 4 -> 3 -> 2 -> 5

Approach:
    - Traverse until you reach the node just before position left (let's call it prevLeft).
    - Reverse the sublist from left to right.
    - Connect the reversed part back into the list.

    We'll use pointer manipulation:
        - ``prevLeft`` — node before the sublist
        - ``start`` — first node in the sublist
        - ``then`` — node following start (used to reverse links iteratively)

**Code Implementation:**

.. code-block:: java

    public void reverseBetween(int left, int right) {
        if (head == null || left == right) return;

        Node dummy = new Node(0);   // Dummy node before head
        dummy.next = head;
        Node prev = dummy;

        // Step 1: Move prev to one node before 'left'
        for (int i = 0; i < left - 1; i++) {
            prev = prev.next;
        }

        // Step 2: Start reversing sublist
        Node start = prev.next;      // First node in the sublist
        Node then = start.next;      // Node to be reversed next

        // Reverse from left to right
        for (int i = 0; i < right - left; i++) {
            start.next = then.next;
            then.next = prev.next;
            prev.next = then;
            then = start.next;
        }

        // Step 3: Update head if reversed from position 1
        head = dummy.next;
    }    

Swap Nodes in Pairs
********************************************

Problem Statement:
    Given a linked list, swap every two adjacent nodes and return its head.

Example:
    - **Input:** LinkedList: 1 -> 2 -> 3 -> 4
    - **Output:** LinkedList: 2 -> 1 -> 4 -> 3

Approach:   
We'll solve this iteratively and also show a recursive version.

**✅ Iterative Solution**

1. Use a ``dummy`` node before the ``head`` to simplify edge cases.

2. Keep track of three pointers:
    - ``prev`` — node before the current pair
    - ``first`` — first node of the pair
    - ``second`` — second node of the pair

3. Swap ``first`` and ``second`` by adjusting links:
    .. code-block:: java

        prev.next = second
        first.next = second.next
        second.next = first

4. Move ``prev`` two steps forward for the next pair.

**Code Implementation:**

.. code-block:: java

    public void swapPairs(ListNode head) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;

        ListNode prev = dummy;

        while (head != null && head.next != null) {
            ListNode first = head;
            ListNode second = head.next;

            // Swapping
            prev.next = second;
            first.next = second.next;
            second.next = first;

            // Move pointers
            prev = first;
            head = first.next;
        }

        return dummy.next;
    }    