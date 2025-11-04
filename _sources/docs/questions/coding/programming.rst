Coding Problems
===============

1. How do you reverse a string in Java?
---------------------------------------

There is no reverse() utility method in the String class. However, you can create a character array from the string and then iterate it from the end to the start. You can append the characters to a string builder and finally return the reversed string.

.. code-block:: java

  public class Main {
      public static void main(String[] args) {
        String strToReverse = "Revanna";
        reverseString(strToReverse);
      }

      private static void reverseString(String strValue) {
        if(strValue == null) {
              throw new IllegalArgumentException("Null is not a valid input!");
        }

        StringBuilder sb = new StringBuilder(strValue.length());
        char[] chars = strValue.toCharArray();

        for (int i = chars.length - 1 ; i >= 0 ; i--) {
              sb.append(chars[i]);
        }

        System.out.println(sb.toString());
        
      }
  }

  // Output:
  // annaveR


2. Swapping of two numbers without using third variable?
--------------------------------------------------------

.. code-block:: java

  public class Main {
      public static void main(String[] args) {
            int a = 10;
            int b = 20;

            System.out.println("Before swapping: a = " + a + ", b = " + b);
            swap(a, b);
      }

      private static void swap(int a, int b) {
            a = a + b; // Step 1: Add both numbers
            b = a - b; // Step 2: Subtract the new value of 'a' with 'b' to get original 'a'
                    // a + b - b = a assigns b
            a = a - b; // Step 3: Subtract the new value of 'b' from 'a' to get original 'b'
                    // a + b - a = b -> a

            System.out.println("After swapping: a = " + a + ", b = " + b);
      }
  }
      b = a - b; 
      a = a - b; // a + b - a = b -> a
  // Output:
  // Before swapping: a = 10, b = 20
  // After swapping: a = 20, b = 10


3. Count the number of vowels in a string?
-------------------------------------------

.. code-block:: java

  public class Main {
      public static void main(String[] args) {

            String name = "Revanna";

            Map<Character, Long> vowelCountMap = name.toLowerCase()
                  .chars()
                  .mapToObj(c -> (char) c)
                  .filter(c -> "aeiou".indexOf(c) != -1)
                  .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));

            System.out.println("Count of Each Vowels found: ");
            System.out.println(vowelCountMap);
            long vowelsCount = vowelCountMap.values().stream().mapToLong(Long::longValue).sum();
            System.out.println("Total Count : " + vowelsCount);
      }
  }

  // Output:
  // Count of Each Vowels found:  
  // {a=2, e=1}
  // Total Count : 3

4. Write a Program to count the number of chars in a string.
------------------------------------------------------------

.. code-block:: java

  public class CharCountDemo {
      public static void countCharacters(String str) {
        System.out.println("-------------------Char Counts without using built-in DataStructure----------------------");
        int[] charCount = new int[256];

        for (int i = 0; i < str.length(); i++) {
              char c = str.charAt(i);
              charCount[c]++;
        }

        for (int i = 0; i < 256; i++) {
              if(charCount[i] > 0) {
                  char c = (char) i;
                  int count = charCount[i];
                  System.out.println(c + " -> " + count);
              }
        }
        System.out.println("-----------------------------------------------------------------------------------------");
      }

      public static void countCharactersWithMap(String str) {
        System.out.println("-------------------Char Counts using built-in Map----------------------------------------");
        Map<String, Integer> charCount = new HashMap<>();

        String[] charsArray = str.split("");

        for (String s : charsArray) {
              charCount.put(s, charCount.getOrDefault(s, 0) + 1);
        }

        charCount.forEach((key, value) -> System.out.println(key + " -> " + value));
        System.out.println("-----------------------------------------------------------------------------------------");
      }

      public static void countCharacterWithStreams(String str) {
        System.out.println("-------------------Char Counts using Streams API-----------------------------------------");
        Arrays.stream(str.split(""))
                  .collect(Collectors.groupingBy(c -> c, Collectors.counting()))
                  .forEach((key, value) -> {
                    System.out.println(key + " -> " + value);
                  });
        System.out.println("-----------------------------------------------------------------------------------------");
      }

      public static void main(String[] args)
      {
        String str = "Revanna";
        countCharacters(str);
        countCharactersWithMap(str);
        countCharacterWithStreams(str);
      }
  }

  // Output
  -------------------Char Counts without using built-in DataStructure----------------------
  R -> 1
  a -> 2
  e -> 1
  n -> 2
  v -> 1
  -----------------------------------------------------------------------------------------
  -------------------Char Counts using built-in Map----------------------------------------
  a -> 2
  R -> 1
  e -> 1
  v -> 1
  n -> 2
  -----------------------------------------------------------------------------------------
  -------------------Char Counts using Streams API-----------------------------------------
  a -> 2
  R -> 1
  e -> 1
  v -> 1
  n -> 2
  -----------------------------------------------------------------------------------------

5. Reverse a String
---------------------

.. code-block:: java

  import java.util.Arrays;
  import java.util.stream.Collectors;
  import java.util.stream.IntStream;

  public class App
  {
      public static void reverseStringWithoutUsingBuiltInMethod(String str) {
            String reversed = "";

            for (int i = str.length()-1; i >= 0; i--) {
              reversed += str.charAt(i);
            }

            System.out.println("Original String: " + str);
            System.out.println("Reversed String: " + reversed);
      }

      public static void reverseStringUsingStringBuilder(String str) {
            StringBuilder reversed = new StringBuilder();

            for (int i = str.length()-1; i >= 0; i--) {
              reversed.append(str.charAt(i));
            }

            System.out.println("Original String: " + str);
            System.out.println("Reversed String: " + reversed);
      }

      public static void reverseStringUsingStringBuilderReversedMethod(String str) {
            StringBuilder sbStr = new StringBuilder(str);

            System.out.println("Original String: " + str);
            System.out.println("Reversed String: " + sbStr.reverse());
      }

      public static void reverseStringUsingStringBuilderAndStream(String str) {

            String reversed1 = Arrays.stream(str.split("")).reduce((a, b) -> b + a).orElseGet(() -> "Nothing");

            String reversed2 = IntStream.rangeClosed(1, str.length()).mapToObj(i -> str.substring(str.length() - i, str.length() - i + 1)).collect(Collectors.joining());

            System.out.println("Original String: " + str);
            System.out.println("Reversed String: " + reversed1);
            System.out.println("Reversed String: " + reversed2);
      }



      public static void main(String[] args) {
            String str = "Revanna";
            reverseStringWithoutUsingBuiltInMethod(str);
            reverseStringUsingStringBuilder(str);
            reverseStringUsingStringBuilderReversedMethod(str);
            reverseStringUsingStringBuilderAndStream(str);
      }
  }

  // Output
  Original String: Revanna
  Reversed String: annaveR

6. String Palindrome Check
--------------------------

.. code-block:: java

  public class App {

      public static void isPalindrome(String str) {
            String stringReversed = new StringBuilder(str.toLowerCase()).reverse().toString();
            System.out.println(str.toLowerCase().equals(stringReversed) ? str + " is Palindrome" : str + " is not Palindrome");
      }

      public static boolean usingWhile() {
          String name = "MADAM".toUpperCase();

          int left = 0;
          int right = name.length() - 1;

          while (left < right) {
              if(name.charAt(left) != name.charAt(right)) {
                  return false;
              }
              left++;
              right--;
          }
          return true;
      }
      
      public static void main(String[] args) {
        isPalindrome("Malayalam");
        isPalindrome("Kannada");

        // Most efficient way
        System.out.println(usingWhile());
  }

  // Output
  Malayalam is Palindrome
  Kannada is not Palindrome

7. Factorial of a number
-------------------------
.. code-block:: java

  public class App {

      public static int factorial(int n) {
        return (n==0 || n==1) ? 1 : n * factorial(n - 1);
      }

      public static void main(String[] args) {
        System.out.println("Factorial of 3: " + factorial(3));
        System.out.println("Factorial of 5: " + factorial(5));
      }
  }

  // Output
  Factorial of 3: 6
  Factorial of 5: 120     

8. Fibonacci Series
----------------------
.. code-block:: java

  public class App {
      public static void main(String[] args) {

        int fiboCount = 10;
        int a = 0, b = 1;

        for (int i = 0; i < fiboCount; i++) {
            System.out.print(a + " ");
            int next = a + b;
            a = b;
            b = next;
        }
      }
  }

  // Output
  0 1 1 2 3 5 8 13 21 34


9. Find the First Non-Repeating Character in a String (using Streams)
---------------------------------------------------------------------
By default, Collectors.groupingBy() uses a HashMap, which does not preserve insertion order.

But since we want the first non-repeated character in the original order, we need to preserve the order of appearance of characters.

That's exactly what LinkedHashMap does — it keeps the insertion order of keys.

.. code-block:: java

  public class FirstNonRepeated {
      public static void main(String[] args) {
        String input = "swiss";

        Character result = input.chars()
                  .mapToObj(c -> (char) c)
                  .collect(Collectors.groupingBy(Function.identity(), LinkedHashMap::new, Collectors.counting()))
                  .entrySet()
                  .stream()
                  .filter(e -> e.getValue() == 1)
                  .map(Map.Entry::getKey)
                  .findFirst()
                  .orElse(null);

        System.out.println("First non-repeated character: " + result);
      }
  }

  // Output
  First non-repeated character: w

10. Find Intersection of Two Arrays
-----------------------------------

.. code-block:: java

  import java.util.*;

  public class Intersection {
      public static void main(String[] args) {
        int[] a = {1, 2, 3, 4};
        int[] b = {3, 4, 5, 6};

        // to store all the elements of first array
        Set<Integer> set = new HashSet<>(); 

        // to store only matching elements
        Set<Integer> result = new HashSet<>();

        for (int num : a) {
          set.add(num);
        }
        for (int num : b) {
          if (set.contains(num)) result.add(num);
        }

        System.out.println(result);
      }
  }

  // Output
  [3, 4]

11. Stream Grouping by Custom Condition (Advanced)
----------------------------------------------------
.. code-block:: java

  class Employee {
      String name;
      String dept;
      Employee(String name, String dept) {
        this.name = name;
        this.dept = dept;
      }
  }

  public class GroupByExample {
      public static void main(String[] args) {
        List<Employee> list = List.of(
                  new Employee("John", "HR"),
                  new Employee("Jane", "IT"),
                  new Employee("Mike", "IT"),
                  new Employee("Sara", "HR")
        );

        Map<String, List<Employee>> grouped = list.stream()
                  .collect(Collectors.groupingBy(e -> e.dept));

        grouped.forEach((dept, employees) -> {
              System.out.println(dept + " => " +
                    employees.stream().map(e -> e.name).collect(Collectors.joining(", ")));
        });
      }
  }

12. Thread Synchronization Example
----------------------------------
.. code-block:: java

  class Counter {
      private int count = 0;

      public synchronized void increment() {
        count++;
      }

      public int getCount() { return count; }
  }

  public class SyncExample {
      public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();

        Thread t1 = new Thread(() -> {
              for (int i = 0; i < 1000; i++) counter.increment();
        });

        Thread t2 = new Thread(() -> {
              for (int i = 0; i < 1000; i++) counter.increment();
        });

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        System.out.println("Final Count: " + counter.getCount());
      }
  }

13. Find Most Frequent Element in a List
------------------------------------------
.. code-block:: java

  public class MostFrequent {
      public static void main(String[] args) {
        List<String> list = Arrays.asList("apple", "banana", "apple", "orange", "banana", "apple");

        String mostFrequent = list.stream()
                  .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()))
                  .entrySet().stream()
                  .max(Map.Entry.comparingByValue())
                  .map(Map.Entry::getKey)
                  .orElse(null);

        System.out.println("Most frequent: " + mostFrequent);
      }
  }

  // Output
  Most frequent: apple

14. Parallel Processing using CompletableFuture
------------------------------------------------
.. code-block:: java

  import java.util.concurrent.*;

  public class AsyncExample {
      public static void main(String[] args) throws Exception {
        CompletableFuture<String> api1 = CompletableFuture.supplyAsync(() -> {
              sleep(1000);
              return "Data from API 1";
        });

        CompletableFuture<String> api2 = CompletableFuture.supplyAsync(() -> {
              sleep(1200);
              return "Data from API 2";
        });

        CompletableFuture<String> combined = api1.thenCombine(api2, (a, b) -> a + " | " + b);
        System.out.println(combined.get()); // Wait for both
      }

      static void sleep(int ms) {
        try { Thread.sleep(ms); } catch (InterruptedException ignored) {}
      }
  }

15. Find the Second Highest Number using Streams
------------------------------------------------
.. code-block:: java

  public class SecondHighestStream {
      public static void main(String[] args) {
        List<Integer> list = Arrays.asList(10, 20, 35, 40, 50, 50);

        int secondHighest = list.stream()
                  .distinct()
                  .sorted(Comparator.reverseOrder())
                  .skip(1)
                  .findFirst()
                  .orElseThrow();

        System.out.println("Second Highest: " + secondHighest);
      }
  }

16. Sort Employees by Salary using Streams
-------------------------------------------
.. code-block:: java

  class Employee {
      String name;
      double salary;

      Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
      }
  }

  public class SortEmployees {
      public static void main(String[] args) {
        List<Employee> employees = List.of(
                  new Employee("Alice", 60000),
                  new Employee("Bob", 90000),
                  new Employee("Charlie", 40000)
        );

        List<Employee> sorted = employees.stream()
                  .sorted(Comparator.comparingDouble(e -> e.salary))
                  .collect(Collectors.toList());

        sorted.forEach(e -> System.out.println(e.name + " : " + e.salary));
      }
  }

  // Output
  Charlie : 40000.0
  Alice : 60000.0
  Bob : 90000.0

17. Count no. of Alphabets, Digits and Special Chars in a String
----------------------------------------------------------------
.. code-block:: java

  public static void countUsingStreams(String str) {
      long alphabets = str.chars().filter(Character::isLetter).count();
      long digits = str.chars().filter(Character::isDigit).count();
      long specialChars = str.chars().filter(c -> !Character.isLetterOrDigit(c)).count();

      System.out.println("Alphabets: " + alphabets);
      System.out.println("Digits: " + digits);
      System.out.println("Special Characters: " + specialChars);
  }

  public static void main(String[] args) {
      String input = "Hello123!@#";
      countAlphabetsDigitsSpecialChars(input);
  }

  // Output
  Alphabets: 5
  Digits: 3
  Special Characters: 3

18. How to implement Stack in Java?
-----------------------------------
A stack is a linear data structure that follows the Last-In-First-Out (LIFO) principle.

**Array-Based Stack Implementation**

.. code-block:: java

  public class ArrayStack {
      private int maxSize;
      private int[] stackArray;
      private int top;

      public ArrayStack(int size) {
        this.maxSize = size;
        this.stackArray = new int[size];
        this.top = -1;
      }

      public void push(int value) {
        if (top == maxSize - 1) {
              throw new RuntimeException("Stack overflow");
        }
        stackArray[++top] = value;
      }

      public int pop() {
        if (top == -1) {
              throw new RuntimeException("Stack underflow");
        }
        return stackArray[top--];
      }

      public int peek() {
        if (top == -1) {
              throw new RuntimeException("Stack is empty");
        }
        return stackArray[top];
      }

      public boolean isEmpty() {
        return top == -1;
      }
  }

  // Usage
  ArrayStack stack = new ArrayStack(5);
  stack.push(10);
  stack.push(20);
  System.out.println(stack.pop());  // Outputs 20
  System.out.println(stack.peek()); // Outputs 10

**Linked List-Based Stack Implementation**

.. code-block:: java
  
  class Node {
      int data;
      Node next;

      Node(int data) {
        this.data = data;
        this.next = null;
      }
  }

  public class LinkedListStack {
      private Node top;

      public LinkedListStack() {
        this.top = null;
      }

      public void push(int value) {
        Node newNode = new Node(value);
        newNode.next = top;
        top = newNode;
      }

      public int pop() {
        if (top == null) {
              throw new RuntimeException("Stack underflow");
        }
        int value = top.data;
        top = top.next;
        return value;
      }

      public int peek() {
        if (top == null) {
              throw new RuntimeException("Stack is empty");
        }
        return top.data;
      }

      public boolean isEmpty() {
        return top == null;
      }
  }

  // Usage
  LinkedListStack stack = new LinkedListStack();
  stack.push(10);
  stack.push(20);
  System.out.println(stack.pop());  // Outputs 20
  System.out.println(stack.peek()); // Outputs 10

19. Depth-First Search (DFS)
-----------------------------

.. note::

  JPMC

Given a "father" node, you recursively collect all its direct 
and indirect children (descendants) by traversing the relation map for each key.

.. code-block:: java

  public class DescendantsFinder {
      public static Set<String> getDescendants(String father, Map<String, List<String>> relations) {
          Set<String> descendants = new LinkedHashSet<>();
          dfs(father, relations, descendants);
          descendants.remove(father); // remove the root, if you want only descendants
          return descendants;
      }

      private static void dfs(String node, Map<String, List<String>> relations, Set<String> result) {
          if (!result.contains(node)) {
              result.add(node);
              // Get the direct children, or empty list if none
              for (String child : relations.getOrDefault(node, Collections.emptyList())) {
                  dfs(child, relations, result);
              }
          }
      }

      public static void main(String[] args) {
          Map<String, List<String>> relations = new HashMap<>();
          relations.put("J", List.of("A", "B"));
          relations.put("A", List.of("C", "D"));
          relations.put("C", List.of("F"));
          relations.put("F", List.of("Z"));

          System.out.println("J -> " + getDescendants("J", relations)); // [A, B, C, D, F, Z]
          System.out.println("A -> " + getDescendants("A", relations)); // [C, D, F, Z]
          System.out.println("C -> " + getDescendants("C", relations)); // [C, D, F, Z]
          System.out.println("F -> " + getDescendants("F", relations)); // [Z]

          /*
          Output:
          J -> [A, C, F, Z, D, B]
          A -> [C, F, Z, D]
          C -> [F, Z]
          F -> [Z]
          */
      }
  }

20. Sum of Two
-----------------
Printing all distinct pairs of numbers from a list 
that sum up to a particular target value

.. code-block:: java

  public class App {

      public static void printPairsWithSum(int[] nums, int target) {
          Set<Integer> seen = new HashSet<>();
          Set<String> printedPairs = new HashSet<>();

          for (int num : nums) {
              int complement = target - num;
              if (seen.contains(complement)) {
                  // Arrange pair so that smaller number is first for uniformity
                  int small = Math.min(num, complement);
                  int large = Math.max(num, complement);
                  String pair = small + "," + large;
                  if (!printedPairs.contains(pair)) {
                      System.out.println("(" + small + ", " + large + ")");
                      printedPairs.add(pair);
                  }
              }
              seen.add(num);
          }
      }

      public static void main(String[] args) {
          int[] numbers = {1, 5, 7, 5, 3, 4, 2};
          int targetSum = 6;
          printPairsWithSum(numbers, targetSum);
      }
  }

  /*
  Output:
  (1, 5)
  (-1, 7)
  (2, 4)
  */

**If you are allowed to short.**

.. code-block:: java

  public class TwoPointerPairs {
      public static void main(String[] args) {
          int[] arr = {10, 0, 100, 90, 60, 40, 80, 20};
          int target = 100;

          Arrays.sort(arr);
          int left = 0, right = arr.length - 1;

          while (left < right) {
              int sum = arr[left] + arr[right];

              if (sum == target) {
                  System.out.println("(" + arr[left] + ", " + arr[right] + ")");
                  left++;
                  right--;
              } else if (sum < target) {
                  left++;
              } else {
                  right--;
              }
          }
      }
  }

21. Remove duplicates from a sorted array.
------------------------------------------

.. code-block:: java

  public class RemoveDuplicates {
      public static void main(String[] args) {
          int[] arr = {1, 2, 2, 3, 4, 4};
          
          twoPointerApproach(arr);
          usingStream(arr);
          usingSet(arr);
      }

      private static void usingSet(int[] arr) {
          LinkedHashSet<Integer> uniqueElements = new LinkedHashSet<>();
          for (int i : arr) {
              uniqueElements.add(i);
          }
          System.out.print(uniqueElements);
      }

      private static void usingStream(int[] arr) {
          String uniqueElements = Arrays
              .stream(arr)
              .boxed()
              .distinct()
              .map(String::valueOf)  // convert Integer -> String
              .collect(Collectors.joining(", ", "[", "]"));

          System.out.println(uniqueElements);
      }

      private static void twoPointerApproach(int[] arr) {
          int length = arr.length;
          int j = 0;
          for (int i = 1; i < length; i++) {
              if (arr[i] != arr[j]) {  // found a new unique element
                  j++;
                  arr[j] = arr[i];
              }
          }

          // print only unique part
          for (int i = 0; i <= j; i++) {
              System.out.print(arr[i] + ", ");
          }
          System.out.println();
      }
  }




22. New  One