# Coding Problems on Streams

## Group according to length of string using stream

:::{admonition} Problem Statement
:class: attention

- Input: ["apple","banana","pear","kiwi"]
- Expected output: 4=[pear,kiwi] , 5=[apple],6=[banana]
  :::

::::{tab-set}
:::{tab-item} Code

```java
public static void main(String[] args) {
    String[] fruits = {"apple","banana","pear","kiwi"};

    Map<Integer, List<String>> numToString = Arrays
        .stream(fruits)
        .collect(Collectors.groupingBy(String::length));

    System.out.println(numToString);
}
```

:::
:::{tab-item} Output

```{code-block} text
Output goes here

```

:::
::::

---

## Stream Operations

:::{admonition} Problem Statement
:class: attention
Given a list and ask :

- Find list of students whose first name starts with alphabet A
- Group The Student By Department Names
- Find all departments names
- Find the count of student in each department
- Find the list of students and sort them by their rank
  :::

::::{tab-set}
:::{tab-item} Code

```java
public class App {
    public static void main(String[] args) {
        List<Student> list = Arrays.asList(
            new Student(1, "Rohit", "Mall", 30, "Male", "Mechanical Engineering", 2015, "Mumbai", 122),
            new Student(2, "Pulkit", "Singh", 56, "Male", "Computer Engineering", 2018, "Delhi", 67),
            new Student(3, "Ankit", "Patil", 25, "Female", "Mechanical Engineering", 2019, "Kerala", 164),
            new Student(4, "Satish Ray", "Malaghan", 30, "Male", "Mechanical Engineering", 2014, "Kerala", 26),
            new Student(5, "Roshan", "Mukd", 23, "Male", "Biotech Engineering", 2022, "Mumbai", 12),
            new Student(6, "Chetan", "Star", 24, "Male", "Mechanical Engineering", 2023, "Karnataka", 90),
            new Student(7, "Arun", "Vittal", 26, "Male", "Electronics Engineering", 2014, "Karnataka", 324),
            new Student(8, "Nam", "Dev", 31, "Male", "Computer Engineering", 2014, "Karnataka", 433),
            new Student(9, "Sonu", "Shankar", 27, "Female", "Computer Engineering", 2018, "Karnataka", 7),
            new Student(10, "Shubham", "Pandey", 26, "Male", "Instrumentation Engineering", 2017, "Mumbai", 98));

            // a. Find list of students whose first name starts with alphabet A
            String startsWithA = list.stream()
                .map(s -> s.fname + " " + s.lname)
                .filter(name -> name.startsWith("A"))
                .collect(Collectors.joining(","));
            System.out.println("Student Names who's name starts with A: " + startsWithA);

            // b. Group The Student By Department Names
            list.stream()
                .collect(Collectors.groupingBy(student -> student.dept))
                .forEach((dept, students) -> {
                    System.out.print(dept + ": ");
                    System.out.println(students.stream().map(student -> student.fname + " " + student.lname).collect(Collectors.joining(", ")));
                });

            // c. Find all departments names
            list.stream().map(s -> s.dept).distinct().forEach(System.out::println);

            // d. Find the count of student in each department
            Map<String, Long> deptwiseCount = list.stream().collect(Collectors.groupingBy(s->s.dept, Collectors.counting()));
            System.out.println(deptwiseCount);

            // e. Find the list of students and sort them by their rank
            list.stream().sorted(Comparator.comparingInt(s -> s.rank)).forEach(student -> {
                System.out.println(student.rank+" -> "+student.fname + " " +student.lname);
            });
            System.out.println(list);
      }
}

class Student {
    int id;
    String fname;
    String lname;
    int age;
    String gender;
    String dept;
    int yop;
    String city;
    int rank;

    public Student(int id, String fname, String lname, int age, String gender, String dept, int yop, String city, int rank) {
        this.id = id;
        this.fname = fname;
        this.lname = lname;
        this.age = age;
        this.gender = gender;
        this.dept = dept;
        this.yop = yop;
        this.city = city;
        this.rank = rank;
    }

}
```

:::
:::{tab-item} Output

```{code-block} text
Output goes here

```

:::
::::

---

## Longest common prefix

:::{admonition} Problem Statement
:class: attention
Write a function to find the longest common prefix string amongst an array of strings.
If there is no common prefix, return an empty string
:::

::::{tab-set}
:::{tab-item} Code

```java
public class LongestCommonPrefix {
    public static String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        }

        // Start with the first string as the prefix
        String prefix = strs[0];

        // Compare with each string
        for (int i = 1; i < strs.length; i++) {
            // Reduce prefix until it matches the start of strs[i]
            while (strs[i].indexOf(prefix) != 0) {
                prefix = prefix.substring(0, prefix.length() - 1);
                if (prefix.isEmpty()) return "";
            }
        }
        return prefix;
    }

    public static void main(String[] args) {
        String[] strs = {"flower", "flow", "flight"};
        System.out.println("Longest Common Prefix: " + longestCommonPrefix(strs));
    }
}

```

:::
:::{tab-item} Output

```{code-block} text
Longest Common Prefix: fl

```

:::
::::

---

## Threads to Print Even/Odd Numbers

:::{admonition} Problem Statement
:class: attention
problem statements
:::

::::{tab-set}
:::{tab-item} Code

```java
public class ExtendThreadClass implements Runnable {

    static int count = 1;
    Object object;

    public ExtendThreadClass(Object object) {
        this.object = object;
    }

    @Override
    public void run() {

        while (count <= 100) {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
            if (count % 2 == 0 && Thread.currentThread().getName().equals("even")) {
                synchronized (object) {
                    System.out.println("Thread Name : " + Thread.currentThread().getName() + " value :" + count);
                    count++;
                    try {
                        object.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
            if (count % 2 != 0 && Thread.currentThread().getName().equals("odd")) {
                synchronized (object) {
                    System.out.println("Thread Name : " + Thread.currentThread().getName() + " value :" + count);
                    count++;
                    object.notify();
                }
            }

        }

    }

    public static void main(String[] args) {

        Object lock=new Object();
        Runnable r1=new ExtendThreadClass(lock);
        Runnable r2=new ExtendThreadClass(lock);
        new Thread(r1, "even").start();
        new Thread(r2, "odd").start();
    }

}
```

:::
:::{tab-item} Output

```{code-block} text
Thread Name : odd value :1
Thread Name : even value :2
Thread Name : odd value :3
Thread Name : even value :4
Thread Name : odd value :5
Thread Name : even value :6
Thread Name : odd value :7
Thread Name : even value :8
Thread Name : odd value :9
Thread Name : even value :10
Thread Name : odd value :11

```

:::
::::

---

## Problem Title

:::{admonition} Problem Statement
:class: attention
Split the data by given position and sort it in ascending order based on date parameter given Pos 0 -3 char is Currency Pos 3 -11 char is Date in YYYYMMDD format Pos 11 - 16 char is User ID

e.g.

- EUR20230510SMTDXX
- USD20180510SWKDMT
- SGD20100928TW1XTZ
- INR20170302STW1XX
- USD20150828SZWDTB
- GBP20200131SUBTXD
  :::

::::{tab-set}
:::{tab-item} Code

```java
import java.util.*;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

class Record {
    String currency;
    LocalDate date;
    String userId;
    String original;

    public Record(String currency, LocalDate date, String userId, String original) {
        this.currency = currency;
        this.date = date;
        this.userId = userId;
        this.original = original;
    }
}

public class DemoClass {
    public static void main(String[] args) {

        List<String> data = Arrays.asList(
            "EUR20230510SMTDXX",
            "USD20180510SWKDMT",
            "SGD20100928TW1XTZ",
            "INR20170302STW1XX",
            "USD20150828SZWDTB",
            "GBP20200131SUBTXD"
                                         );

        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd");
        List<Record> records = new ArrayList<>();

        // Split each record by position
        for (String str : data) {
            String currency = str.substring(0, 3);
            String dateStr = str.substring(3, 11); // 8 chars for date
            String userId = str.substring(11);     // remaining part
            LocalDate date = LocalDate.parse(dateStr, formatter);
            records.add(new Record(currency, date, userId, str));
        }

        // Sort by date ascending
        records.sort(Comparator.comparing(r -> r.date));

        // Print sorted output
        System.out.println("Sorted Records by Date:");
        for (Record r : records) {
            System.out.println(r.original);
        }
    }
}
```

:::
:::{tab-item} Output

```{code-block} text
Sorted Records by Date:
SGD20100928TW1XTZ
USD20150828SZWDTB
INR20170302STW1XX
USD20180510SWKDMT
GBP20200131SUBTXD
EUR20230510SMTDXX

```

:::
::::

---

## Permutations

:::{admonition} Problem Statement
:class: attention
problem statements
:::

::::{tab-set}
:::{tab-item} Code

```java
public class PermutationExample {

    static void permute(String str, String result) {
        if (str.isEmpty()) {
            System.out.println(result);
            return;
        }

        for (int i = 0; i < str.length(); i++) {
            char ch = str.charAt(i);
            String remaining = str.substring(0, i) + str.substring(i + 1);
            permute(remaining, result + ch);
        }
    }

    public static void main(String[] args) {
        String input = "ABC";
        permute(input, "");
    }
}

```

:::
:::{tab-item} Output

```{code-block} text
ABC
ACB
BAC
BCA
CAB
CBA

```

:::
::::

---

## Combinations

:::{admonition} Problem Statement
:class: attention
problem statements
:::

::::{tab-set}
:::{tab-item} Code

```java
public class CombinationExample {

    static void combine(String str, String result, int index) {
        // Print every non-empty combination
        if (!result.isEmpty()) {
            System.out.println(result);
        }

        // Explore all next possible characters
        for (int i = index; i < str.length(); i++) {
            combine(str, result + str.charAt(i), i + 1);
        }
    }

    public static void main(String[] args) {
        String input = "ABC";
        combine(input, "", 0);
    }
}

```

:::
:::{tab-item} Output

```{code-block} text
A
AB
AC
B
BC
C

```

:::
::::

---

## Join Streams/Concat Streams/Union of Two Lists

:::{admonition} Problem Statement
:class: attention
problem statements
:::

::::{tab-set}
:::{tab-item} Code

```java
public class UnionOfTwoLists {

    public static void main(String[] args) {
        List<Integer> list1 = Arrays.asList(1, 2, 3, 4, 5);
        List<Integer> list2 = Arrays.asList(4, 5, 6, 7, 8);

        List<Integer> union = Stream.concat(list1.stream(), list2.stream()).distinct().sorted(Comparator.reverseOrder()).toList();
        System.out.println(union);
    }
}
```

:::
:::{tab-item} Output

```{code-block} text
[8, 7, 6, 5, 4, 3, 2, 1]

```

:::
::::

---

## Given a list of integers, find all numbers divisible by 3 and 5

::::{tab-set}
:::{tab-item} Code

```java
public static void main( String[] args ) {
    List<Integer> integerList = IntStream.rangeClosed(0,50)
        .filter(num -> num % 3 == 0 && num % 5 == 0)
        .boxed()
        .toList();
    System.out.println(integerList);
}
```

:::
:::{tab-item} Output

```{code-block} text
[0, 15, 30, 45]
```

:::
::::

---

## Given a list of strings, remove all empty or null values.

::::{tab-set}
:::{tab-item} Code

```java
public static void main( String[] args ) {
    List<String> strings = Arrays.asList("Java", "", null, "Spring", " ", "Boot", null, "Streams");

    List<String> filtered = strings.stream()
        .filter(Objects::nonNull)
        .map(String::trim)
        .filter(str -> !str.isEmpty())
        .toList();

    System.out.println(filtered);
}
```

:::
:::{tab-item} Output

```{code-block} text
[Java, Spring, Boot, Streams]
```

:::
::::

---

## Given a list of integers, create a list of their squares without duplicates.

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {
        List<Integer> numbers = Arrays.asList(2, 3, 4, 2, 5, 3, 6, 4);
        List<Integer> squared = numbers.stream()
            .distinct()
            .map(num -> num * num)
            .toList();
        System.out.println(squared);
    }
```

:::
:::{tab-item} Output

```{code-block} text
[4, 9, 16, 25, 36]

```

:::
::::

---

## Find all words in a list whose length > 5

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {
        List<String> words = Arrays.asList("Spring", "Boot", "Microservices", "Java", "Stream", "Programming");

        List<String> wordsFiltered = words.stream()
            .filter(str -> str.length() > 5)
            .toList();

        System.out.println(wordsFiltered);
    }
```

:::
:::{tab-item} Output

```{code-block} text
[Spring, Microservices, Stream, Programming]

```

:::
::::

---

## Print Prime NUmbers from 1 to 100

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {

        List<Integer> primeNumbers = IntStream.rangeClosed(1, 50)
                .boxed()
                .filter(App::isPrime)
                .toList();
        System.out.println(primeNumbers);
    }

    private static boolean isPrime(Integer value) {
        if (value <= 1) return false;
        return IntStream.rangeClosed(2, (int) Math.sqrt(value))
            .boxed()
            .noneMatch(num -> value % num == 0);
    }
```

:::
:::{tab-item} Output

```{code-block} text
[2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47]

```

:::
::::

---

## Given a list of names, print those ending with “a”

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {

        List<String> names = Arrays.asList("Anita", "Ravi", "Suma", "Kiran", "Lata", "Meena", "Rahul");
        List<String> endsWithA = names.stream()
            .filter(str -> str.endsWith("a") || str.endsWith("A"))
            .toList();

        System.out.println(endsWithA);
    }
```

:::
:::{tab-item} Output

```{code-block} text
[Anita, Suma, Lata, Meena]

```

:::
::::

---

## Given a list of integers, return a new list containing only odd numbers multiplied by 2.

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {

        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        List<Integer> oddNumSquared = numbers.stream()
            .filter(num -> num % 2 != 0)
            .map(num -> num * 2)
            .toList();

        System.out.println(oddNumSquared);
    }
```

:::
:::{tab-item} Output

```{code-block} text
[2, 6, 10, 14, 18]

```

:::
::::

---

## Convert a list of names to title case (e.g., “john” → “John”).

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {
        List<String> names = Arrays.asList("john", "ALICE", "robert", "sOPHIA", "mike");
        List<String> titleCaseNames = names.stream()
            .map(str -> str.substring(0, 1).toUpperCase().concat(str.substring(1).toLowerCase()))
            .toList();
        System.out.println(titleCaseNames);
    }
```

:::
:::{tab-item} Output

```{code-block} text
[John, Alice, Robert, Sophia, Mike]

```

:::
::::

---

## Compute the product of all elements in a list.

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {
        List<Integer> numbers = Arrays.asList(2, 3, 4, 5);

        Integer product = numbers.stream()
            .reduce(1, (num1, num2) -> num1 * num2);
        System.out.println(product);
    }
```

:::
:::{tab-item} Output

```{code-block} text
120
```

:::
::::

---

## Find the minimum number in a list.

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {
        List<Integer> numbers = Arrays.asList(10, 3, 45, 7, 2, 19);

        Optional<Integer> minNumnber = numbers.stream()
            .min(Integer::compareTo);
        minNumnber.ifPresent(System.out::println);
    }
```

:::
:::{tab-item} Output

```{code-block} text
2
```

:::
::::

---

## Find the sum of all even numbers.

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {
        Integer sum = IntStream.rangeClosed(0, 50)
            .boxed()
            .filter(num -> num % 2 == 0)
            .reduce(0, Integer::sum);

        System.out.println(sum.intValue());
    }
```

:::
:::{tab-item} Output

```{code-block} text
650
```

:::
::::

---

## Find the average of all squares of a list of numbers.

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {
        List<Integer> numbers = Arrays.asList(1,2,3,4,5);

        double asDouble = numbers.stream()
            .mapToInt(n -> n * n)
            .average().orElse(0.0);

        System.out.println(asDouble);
    }
```

:::
:::{tab-item} Output

```{code-block} text
13.5
```

:::
::::

---

## Count how many numbers are greater than 100.

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {
        List<Integer> numbers = Arrays.asList(50, 120, 300, 99, 101, 200, 45);

        double count = numbers.stream()
                .filter(num -> num > 100)
                    .count();

        System.out.println(count);
    }
```

:::
:::{tab-item} Output

```{code-block} text
4
```

:::
::::

---

## Find the longest string in a list.

::::{tab-set}
:::{tab-item} Code

```java
    public static void main( String[] args ) {
        List<String> words = Arrays.asList("Java", "SpringBoot", "Microservices", "API", "Stream");

        String longestString = words.stream()
            .filter(Objects::nonNull)
            .map(String::trim)
            .reduce(words.get(0), (s1, s2) -> s1.length() > s2.length() ? s1 : s2);

        System.out.println(longestString);
    }
```

:::
:::{tab-item} Output

```{code-block} text
Microservices
```

:::
::::

---

## Group a list of Employee objects by department name.

::::{tab-set}
:::{tab-item} Code

```java
record Employee(int id, String name, double sal, String dept){}

public class App
{
    public static void main( String[] args ) {
        List<Employee> employees = List.of(
            new Employee(1,"Revs", 120, "IT"),
            new Employee(2, "Anjali", 450, "SALES"),
            new Employee(3, "Sanjana", 451, "IT")
                                          );

        Map<String, Long> empCountByDept = employees.stream()
            .collect(Collectors.groupingBy(Employee::dept, Collectors.counting()));

        System.out.println(empCountByDept);
    }
}
```

:::
:::{tab-item} Output

```{code-block} text
{IT=2, SALES=1}
```

:::
::::

---

## For each department, compute average salary.

::::{tab-set}
:::{tab-item} Code

```java
record Employee(int id, String name, double sal, String dept){}

public class App
{
    public static void main( String[] args ) {
        List<Employee> employees = List.of(
            new Employee(1,"Revs", 1000, "IT"),
            new Employee(2, "Anjali", 1000, "SALES"),
            new Employee(3, "Sanjana", 5000, "IT"),
            new Employee(4, "Manu", 3000, "SALES")
                                          );

        Map<String, Double> empCountByDept = employees.stream()
            .collect(Collectors.groupingBy(
                Employee::dept,
                Collectors.averagingDouble(Employee::sal)));

        System.out.println(empCountByDept);
    }
}
```

:::
:::{tab-item} Output

```{code-block} text
{IT=3000.0, SALES=2000.0}
```

:::
::::

---

## Count employees in each age group (e.g., <30, 30-50, >50).

::::{tab-set}
:::{tab-item} Code

```java
record Employee(int id, String name, int age, double sal, String dept){}

public class App
{
    public static void main( String[] args ) {
        List<Employee> employees = List.of(
            new Employee(1,"Revs", 32, 1000, "IT"),
            new Employee(2, "Anjali", 17, 1000, "SALES"),
            new Employee(3, "Sanjana", 14, 5000, "IT"),
            new Employee(4, "Manu", 8,3000, "SALES"),
            new Employee(5, "Savi", 35, 2000, "SALES")
                                          );

        Map<String, Long> empCountByDept = employees.stream()
            .collect(Collectors.groupingBy(
                e -> getAgeGroup(e.age()),
                Collectors.counting()
        ));
        System.out.println(empCountByDept);
    }

    private static String getAgeGroup(int age) {
        String ageGroup;

        if (age < 10) {
            ageGroup = "Under 10";
        } else if (age < 20) {
            ageGroup = "Under 20";
        } else {
            ageGroup = "Under 30";
        }
        return ageGroup;

    }
}
```

:::
:::{tab-item} Output

```{code-block} text
{Under 30=2, Under 10=1, Under 20=2}
```

:::
::::

---

## Partition employees based on whether they are permanent or contract.

::::{tab-set}
:::{tab-item} Code

```java
record Employee(int id, String name, int age, double sal, String dept, boolean isPermanent){}

public class App
{
    public static void main( String[] args ) {
        List<Employee> employees = List.of(
            new Employee(1,"Revs", 32, 1000, "IT", true),
            new Employee(2, "Anjali", 17, 1000, "SALES", false),
            new Employee(3, "Sanjana", 14, 5000, "IT", true),
            new Employee(4, "Manu", 8,3000, "SALES", false),
            new Employee(5, "Savi", 35, 2000, "SALES", false)
        );

        Map<Boolean, List<Employee>> permanentEmps = employees.stream()
            .collect(Collectors.partitioningBy(Employee::isPermanent, Collectors.toList()));

        System.out.println("Permanent Employees: ");
        permanentEmps.get(true).forEach(employee -> System.out.println(" - " + employee.name()));

        System.out.println("Contract Employees: ");
        permanentEmps.get(false).forEach(employee -> System.out.println(" - " + employee.name()));
    }
}
```

:::
:::{tab-item} Output

```{code-block} text
Permanent Employees:
 - Revs
 - Sanjana
Contract Employees:
 - Anjali
 - Manu
 - Savi

```

:::
::::

---

## Given a list of lists of integers, flatten and find the sum of all numbers.

::::{tab-set}
:::{tab-item} Code

```java
public class FlattenAndSum {
    public static void main(String[] args) {
        List<List<Integer>> numbers = List.of(
            Arrays.asList(1, 2, 3),
            Arrays.asList(4, 5),
            Arrays.asList(6, 7, 8, 9)
        );

        // Flatten and find the sum of all numbers
        int totalSum = numbers.stream()
                .flatMap(List::stream)   // flatten list of lists
                .mapToInt(Integer::intValue)
                .sum();

        System.out.println("Sum of all numbers = " + totalSum);
    }
}
```

:::
:::{tab-item} Output

```{code-block} text
Sum of all numbers = 45
```

:::
::::

---

Add Here

---

# Copy Only - Don't Modify

## Problem Title

:::{admonition} Problem Statement
:class: attention
problem statements
:::

::::{tab-set}
:::{tab-item} Code

```java
paste your code here
```

:::
:::{tab-item} Output

```{code-block} text
Output goes here
```

:::
::::
