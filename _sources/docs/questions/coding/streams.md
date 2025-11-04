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
