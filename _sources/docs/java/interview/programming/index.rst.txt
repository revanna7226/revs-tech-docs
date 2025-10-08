Coding Challenge Questions
==========================

#. How do you reverse a string in Java?

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


#. Swapping of two numbers without using third variable?

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


#. Count the number of vowels in a string?

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

#. Write a Program to count the number of chars in a string.

   .. code-block:: java

      package com.revs;

      import java.util.Arrays;
      import java.util.HashMap;
      import java.util.Map;
      import java.util.stream.Collectors;

      public class CharCountDemo
      {
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

#. Reverse a String

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
               StringBuilder reversed = new StringBuilder(str);

               System.out.println("Original String: " + str);
               System.out.println("Reversed String: " + reversed.reverse());
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

#. String Palindrome Check

   .. code-block:: java

      public class App {

         public static void isPalindrome(String str) {
               String stringReversed = new StringBuilder(str.toLowerCase()).reverse().toString();
               System.out.println(str.toLowerCase().equals(stringReversed) ? str + " is Palindrome" : str + " is not Palindrome");
         }
         
         public static void main(String[] args) {
               isPalindrome("Malayalam");
               isPalindrome("Kannada");
         }
      }

      // Output
      Malayalam is Palindrome
      Kannada is not Palindrome

#. Factorial of a number

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

#. Fibonacci Series

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


#. Find the First Non-Repeating Character in a String (using Streams)

   .. code-block:: java

      import java.util.*;
      import java.util.function.Function;
      import java.util.stream.Collectors;

      public class FirstNonRepeated {
         public static void main(String[] args) {
            String input = "swiss";

            Character result = input.chars()
                     .mapToObj(c -> (char) c)
                     .collect(Collectors.groupingBy(Function.identity(), LinkedHashMap::new, Collectors.counting()))
                     .entrySet().stream()
                     .filter(e -> e.getValue() == 1)
                     .map(Map.Entry::getKey)
                     .findFirst()
                     .orElse(null);

            System.out.println("First non-repeated character: " + result);
         }
      }

      // Output
      First non-repeated character: w

#. Find Intersection of Two Arrays

   .. code-block:: java

      import java.util.*;

      public class Intersection {
         public static void main(String[] args) {
            int[] a = {1, 2, 3, 4};
            int[] b = {3, 4, 5, 6};
            Set<Integer> set = new HashSet<>();
            Set<Integer> result = new HashSet<>();

            for (int num : a) set.add(num);
            for (int num : b) if (set.contains(num)) result.add(num);

            System.out.println(result);
         }
      }

      // Output
      [3, 4]

#. Stream Grouping by Custom Condition (Advanced)

   .. code-block:: java

      import java.util.*;
      import java.util.stream.Collectors;

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

#. Thread Synchronization Example

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

#. Find Most Frequent Element in a List

   .. code-block:: java

      import java.util.*;
      import java.util.function.Function;
      import java.util.stream.Collectors;

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

#. Parallel Processing using CompletableFuture

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

#. Find the Second Highest Number using Streams

   .. code-block:: java

      import java.util.*;
      import java.util.stream.Collectors;

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

#. Sort Employees by Salary using Streams

   .. code-block:: java

      import java.util.*;
      import java.util.stream.Collectors;

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

#. Count no. of Alphabets, Digits and Special Chars in a String

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

