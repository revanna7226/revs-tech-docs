=================================
Big O Notation in Java: Explained
=================================

Introduction
============
    - Big O Notation is a **mathematical representation** that describes the **performance or complexity** of an algorithm.
    - It helps us understand **how the runtime or space requirements of an algorithm grow** as the input size increases.
    - In Java, Big O Notation is widely used to analyze the **efficiency of Data Structures and Algorithms**, especially in the `java.util` package.


Understanding Big O
===================

Big O describes the **upper bound** of an algorithm’s growth rate — i.e., the **worst-case scenario** for execution time or memory usage.

For example:
    - ``O(1)`` → Constant Time
    - ``O(log n)`` → Logarithmic Time
    - ``O(n)`` → Linear Time
    - ``O(n log n)`` → Linearithmic Time
    - ``O(n²)`` → Quadratic Time
    - ``O(2ⁿ)``, ``O(n!)`` → Exponential or Factorial Time

Why Big O Matters
=================

When comparing multiple algorithms or Java data structures, Big O helps you determine which will scale better as your input grows.

For example:
    - ``ArrayList`` is faster for random access (``O(1)``) but slower for insertions (``O(n)``).
    - ``LinkedList`` is better for insertions/removals (``O(1)``) but slower for random access (``O(n)``).

Common Time Complexities
=========================

+--------------------+---------------------------------------+----------------------------------+
| Big O Notation     | Description                           | Example Operation                |
+====================+=======================================+==================================+
| ``O(1)``           | Constant time — independent of input  | Access element by index in       |
|                    |                                       | ``ArrayList``                    |
+--------------------+---------------------------------------+----------------------------------+
| ``O(log n)``       | Logarithmic time — input reduced each | Search in a ``TreeMap`` or       |
|                    | step                                  | ``Binary Search``                |
+--------------------+---------------------------------------+----------------------------------+
| ``O(n)``           | Linear time — grows proportionally    | Traversing a ``LinkedList``      |
|                    |                                       | or looping through an array      |
+--------------------+---------------------------------------+----------------------------------+
| ``O(n log n)``     | Linearithmic time — efficient sorting | ``MergeSort`` or ``QuickSort``   |
|                    | algorithms                            | average case                     |
+--------------------+---------------------------------------+----------------------------------+
| ``O(n²)``          | Quadratic — nested loops over input   | ``BubbleSort``, ``InsertionSort``|
+--------------------+---------------------------------------+----------------------------------+
| ``O(2ⁿ)``,``O(n!)`` | Exponential/Factorial — very slow     | Recursive algorithms (like       |
|                     | for large inputs                      | Traveling Salesman Problem)      |
+--------------------+---------------------------------------+----------------------------------+

Big O with Java Collections Framework
=====================================

Below are the **time complexities of common Java data structures**:

ArrayList
----------

+------------------------+-------------------+
| Operation              | Time Complexity   |
+========================+===================+
| Access (by index)      | ``O(1)``          |
+------------------------+-------------------+
| Insert (end)           | ``O(1)`` amortized|
+------------------------+-------------------+
| Insert (middle/start)  | ``O(n)``          |
+------------------------+-------------------+
| Remove (by index)      | ``O(n)``          |
+------------------------+-------------------+

LinkedList
-----------

+------------------------+-------------------+
| Operation              | Time Complexity   |
+========================+===================+
| Add/Remove (start/end) | ``O(1)``          |
+------------------------+-------------------+
| Access (by index)      | ``O(n)``          |
+------------------------+-------------------+
| Search                 | ``O(n)``          |
+------------------------+-------------------+

HashMap
--------

+------------------------+-------------------+
| Operation              | Average Case      |
+========================+===================+
| Put                    | ``O(1)``          |
+------------------------+-------------------+
| Get                    | ``O(1)``          |
+------------------------+-------------------+
| Remove                 | ``O(1)``          |
+------------------------+-------------------+
| Worst Case (hash coll.)| ``O(n)``          |
+------------------------+-------------------+

TreeMap (Red-Black Tree)
-------------------------

+------------------------+-------------------+
| Operation              | Time Complexity   |
+========================+===================+
| Put                    | ``O(log n)``      |
+------------------------+-------------------+
| Get                    | ``O(log n)``      |
+------------------------+-------------------+
| Remove                 | ``O(log n)``      |
+------------------------+-------------------+

HashSet / TreeSet
------------------

+------------------------+-------------------+
| Data Structure         | Complexity        |
+========================+===================+
| ``HashSet``            | ``O(1)`` average, ``O(n)`` worst |
+------------------------+-------------------+
| ``TreeSet``            | ``O(log n)``      |
+------------------------+-------------------+

Algorithm Examples in Java
==========================

Example 1: Linear Search (``O(n)``)
-----------------------------------

.. code-block:: java

   public static int linearSearch(int[] arr, int target) {
       for (int i = 0; i < arr.length; i++) {
           if (arr[i] == target)
               return i;
       }
       return -1;
   }

- The loop runs ``n`` times → **O(n)** time complexity.

Example 2: Binary Search (``O(log n)``)
---------------------------------------

.. code-block:: java

   public static int binarySearch(int[] arr, int target) {
       int low = 0, high = arr.length - 1;
       while (low <= high) {
           int mid = (low + high) / 2;
           if (arr[mid] == target)
               return mid;
           else if (arr[mid] < target)
               low = mid + 1;
           else
               high = mid - 1;
       }
       return -1;
   }

- The search space is halved each time → **O(log n)**.
- Array should be sorted to apply binary Search.

Example 3: Bubble Sort (``O(n²)``)
----------------------------------

.. code-block:: java

   public static void bubbleSort(int[] arr) {
       for (int i = 0; i < arr.length - 1; i++) {
           for (int j = 0; j < arr.length - i - 1; j++) {
               if (arr[j] > arr[j + 1]) {
                   int temp = arr[j];
                   arr[j] = arr[j + 1];
                   arr[j + 1] = temp;
               }
           }
       }
   }

- Two nested loops → **O(n²)** time complexity.

Space Complexity
================

Big O also describes **space complexity**, i.e., the amount of **extra memory** used by the algorithm.

Examples:

+------------------------+-------------------+
| Algorithm              | Space Complexity  |
+========================+===================+
| ``Bubble Sort``        | ``O(1)``          |
+------------------------+-------------------+
| ``Merge Sort``         | ``O(n)``          |
+------------------------+-------------------+
| ``Recursive Fibonacci``| ``O(n)``          |
+------------------------+-------------------+

Summary Table
==============

+---------------------+------------------------------+
| Complexity Class    | Performance Description       |
+=====================+==============================+
| ``O(1)``            | Excellent                    |
+---------------------+------------------------------+
| ``O(log n)``        | Good                         |
+---------------------+------------------------------+
| ``O(n)``            | Fair                         |
+-------------------j-+------------------------------+
| ``O(n log n)``      | Moderate                     |
+---------------------+------------------------------+
| ``O(n²)``           | Poor                         |
+---------------------+------------------------------+
| ``O(2ⁿ)``, ``O(n!)``| Very Poor (Avoid if possible)|
+---------------------+------------------------------+


Conclusion
===========

Understanding **Big O Notation** is crucial for writing efficient Java programs.  
It helps predict how your application will scale and perform with larger inputs.

When designing algorithms or choosing data structures:
- **Prefer lower complexity** whenever possible.
- Use profiling tools and benchmarking (e.g., ``System.nanoTime()``) to measure performance.

Big O is not about *exact* execution time, but about *growth behavior* —  
and mastering it makes you a more effective and efficient Java developer.
