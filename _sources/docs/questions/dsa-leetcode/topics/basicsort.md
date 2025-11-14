# Basic Sorts

## Bubble Sort

Bubble Sort is a simple comparison-based sorting algorithm.
It repeatedly compares adjacent elements and swaps them if they are in the wrong order.

👉 After each full pass, the largest element bubbles to the end of the array.

---

```java
    int[] values = {10, 5, 15, 3, 7, 13, 17};

    int length = values.length;

    for (int i = length - 1; i > 0; i--) {
        for (int j = 0; j < i; j++) {
            if(values[j] > values[j + 1]) {
                int temp = values[j];
                values[j] = values[j + 1];
                values[j + 1] = temp;
            }
        }
    }

    System.out.println(Arrays.toString(values));
```

---

## Selection Sort

Selection Sort works by repeatedly selecting the smallest element from the unsorted part of the array and placing it at the beginning.

Process:

- Find the minimum element in the unsorted array
- Swap it with the element at the current index
- Move the boundary of the sorted part one step forward
- Repeat

```java
    int[] arr = {64, 25, 12, 22, 11};

    int n = arr.length;

    for (int i = 0; i < n - 1; i++) {
        int minIndex = i;

        // Find the minimum element in the remaining unsorted array
        for (int j = i + 1; j < n; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }

        // Swap the found minimum element with arr[i]
        int temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }
```

## Insertion Sort

Insertion Sort builds the final sorted array one element at a time, just like sorting playing cards in your hand.

Process:

- Start from the second element
- Compare it with elements before it
- Shift larger elements to the right
- Insert the current element in its correct position

```java
    int[] values = {10, 5, 15, 3, 7, 13, 17};

    int length = values.length;

    for (int i = 1; i < length; i++) {
        int temp = values[i];
        int j = i - 1;
        while(j > -1 && temp < values[j]) {
            values[j + 1] = values[j];
            values[j] = temp;
            j--;
        }
    }

    System.out.println(Arrays.toString(values));
```

## Merge Sort

## Quick Sort
