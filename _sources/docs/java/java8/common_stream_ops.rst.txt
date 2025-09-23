Common Stream Operations
===============================

Intermediate Operations
--------------------------

Map()
+++++++++++++++++

* Transforms each element of the stream into another object.
* Returns a stream of **the same size** as the original.

Example 1: Using map() to transform a list of strings to uppercase

.. code-block:: java

    List<String> names = Arrays.asList("Revs", "Push", "Anjali");

    List<String> upperNames = names.stream()
                                .map(String::toUpperCase)
                                .collect(Collectors.toList());

    System.out.println(upperNames);

    // Output: [REVS, PUSH, ANJALI]

Example 2: Using map() to get the length of each string

.. code-block:: java

    List<String> names = Arrays.asList("Revs", "Push", "Anjali");

    List<Integer> lengths = names.stream()
                                .map(String::length)
                                .collect(Collectors.toList());

    System.out.println(lengths);

    // Output: [4, 4, 6]

FlatMap()
+++++++++++++++++

* Used when each element of the stream **itself is a stream or collection**.
* Flattens multiple streams into a **single stream**.
* Useful for avoiding nested streams.

Example 1: Flattening a list of lists

.. code-block:: java

    List<List<String>> listOfLists = Arrays.asList(
        Arrays.asList("A", "B"),
        Arrays.asList("C", "D"),
        Arrays.asList("E", "F")
    );

    List<String> flattenedList = listOfLists.stream()
                                            .flatMap(List::stream)
                                            .collect(Collectors.toList());

    System.out.println(flattenedList);

    // Output: [A, B, C, D, E, F]

Example 2: Splitting strings into characters

.. code-block:: java

    List<String> words = Arrays.asList("Java", "Stream");

    List<String> letters = words.stream()
                                .flatMap(word -> Arrays.stream(word.split("")))
                                .collect(Collectors.toList());

    System.out.println(letters);

    // Output: [J, a, v, a, S, t, r, e, a, m]

**Difference Between map() and flatMap() in Java 8**

.. list-table:: Comparison of map() vs flatMap()
    :header-rows: 1
    :widths: 20 30 30 

    * - Feature
      - Map()
      - FlatMap()
    * - Transformation
      - Transforms each element individually.
      - Transforms each element and flattens nested structures.
    * - Return Type
      - Returns a stream of the same size as the original.
      - Returns a single flattened stream.
    * - Nested Structures
      - May produce nested streams if elements are collections.
      - Flattens nested streams into one stream.
    * - Use Case
      - One-to-one transformations (e.g., convert strings to uppercase, get length).
      - One-to-many transformations (e.g., list of lists to a single stream).
    * - Example
      - ``list.stream().map(String::toUpperCase)``
      - ``listOfLists.stream().flatMap(List::stream)``

MapReduce()
+++++++++++++++++

**Map-Reduce** is a functional programming concept that allows you to **transform** (map) data and then **aggregate** (reduce) it.  
Java 8 Streams provide built-in support for map and reduce operations using **map()** and **reduce()** methods.

* **Map** – Transforms each element of a stream into another form.
* **Reduce** – Combines all elements of a stream into a single result.

**Map-Reduce Pipeline**

1. **Source** – Collection, array, or any data stream.
2. **Map Operation** – Transform elements using ``map()``.
3. **Reduce Operation** – Aggregate results using ``reduce()``.

**Java 8 Stream’s reduce() has two main forms:**

1. **reduce(BinaryOperator<T> accumulator)**

   - Takes a binary operator to combine two elements.
   - Returns an ``Optional<T>`` since the stream may be empty.
   - Use get() to retrieve the value.

2. **reduce(T identity, BinaryOperator<T> accumulator)**
   
   - Takes an identity value and a binary operator.
   - Returns a non-optional result (identity if stream is empty).
   - No need to call get().

.. code-block:: java

    List<Integer> numbers = Arrays.asList(3, 7, 8, 1, 5, 9);

    List<String> words = Arrays.asList("corejava", "spring", "hibernate");

    //Traditional way of sum
    int sum = 0;
    for (int no : numbers) {
        sum = sum + no;
    }
    System.out.println(sum); // Output: 33

    // Sum using Stream API
    int sum1 = numbers.stream().mapToInt(i -> i).sum();
    System.out.println(sum1); // Output: 33

    // Sum using reduce method
    Integer reduceSum = numbers.stream().reduce(0, (a, b) -> a + b);
    System.out.println(reduceSum); // Output: 33

    // Sum using reduce method with method reference
    Optional<Integer> reduceSumWithMethodReference = numbers.stream().reduce(Integer::sum);
    System.out.println(reduceSumWithMethodReference.get()); // Output: 33

    // Product using reduce method
    Integer mulResult = numbers.stream().reduce(1, (a, b) -> a * b);
    System.out.println(mulResult); // Output: 7560

    // Find max value using reduce
    Integer maxvalue = numbers.stream().reduce(0, (a, b) -> a > b ? a : b);
    System.out.println(maxvalue); // Output: 9

    // Find max value using reduce with method reference
    Integer maxvalueWithMethodReference = numbers.stream().reduce(Integer::max).get();
    System.out.println(maxvalueWithMethodReference); // Output: 9

    // Find longest string using reduce
    String longestString = words.stream()
            .reduce((word1, word2) -> word1.length() > word2.length() ? word1 : word2)
            .get();
    System.out.println(longestString); // Output: corejava

    // get employee whose grade A
    // get salary
    double avgSalary = EmployeeDatabase.getEmployees().stream()
            .filter(employee -> employee.getGrade().equalsIgnoreCase("A"))
            .map(employee -> employee.getSalary())
            .mapToDouble(i -> i)
            .average().getAsDouble();
    System.out.println(avgSalary); // Output: 75000.0

    // get total salary of employees whose grade A
    double sumSalary = EmployeeDatabase.getEmployees().stream()
            .filter(employee -> employee.getGrade().equalsIgnoreCase("A"))
            .map(employee -> employee.getSalary())
            .mapToDouble(i -> i)
            .sum();
    System.out.println(sumSalary); // Output: 150000.0

Sorted()
+++++++++++++++++

In Java 8, you can sort a List using **Stream API sorted()** or **List.sort()** with **lambda expressions**.

* Use **List.sort()** or **stream().sorted()** to sort lists.
* Use **Map.entrySet().stream().sorted()** for maps.
* Use **Comparator.comparing()** or **Map.Entry.comparingByKey/value()**.
* Collect sorted results into **LinkedHashMap** to maintain order for Maps.

Example 1: Sorting a List

.. code-block:: java

    List<Integer> list = new ArrayList<>();
    list.add(8);
    list.add(3);
    list.add(12);
    list.add(4);
    List<Employee> employees = DataBase.getEmployees();

    /*Collections.sort(employees, new Comparator<Employee>() {
        @Override
        public int compare(Employee o1, Employee o2) {
            return (int) (o1.getSalary() - o2.getSalary());// ascending
        }
    });*/
    //Lambda expression
    Collections.sort(employees, ( o1,  o2) ->(int) (o1.getSalary() - o2.getSalary()));
    //System.out.println(employees);
    
    //employees.stream().sorted(( o1,  o2) ->(int) (o2.getSalary() - o1.getSalary())).forEach(System.out::println);
    //employees.stream().sorted(Comparator.comparing(emp->emp.getSalary())).forEach(System.out::println);
    
    employees.stream().sorted(Comparator.comparing(Employee::getDept)).forEach(System.out::println);
    /*
    * Collections.sort(list); //ASSENDING Collections.reverse(list);
    * System.out.println(list);
    * 
    * list.stream().sorted(Comparator.reverseOrder()).forEach(s->System.out.println
    * (s));//descending
    */

Example 2: Sorting a Map

.. code-block:: java

    Map<String, Integer> map = new HashMap<>();
    map.put("eight", 8);
    map.put("four", 4);
    map.put("ten", 10);
    map.put("two", 2);

    Map<Employee, Integer> employeeMap = new TreeMap<>((o1, o2) -> (int) (o2.getSalary() - o1.getSalary()));
    employeeMap.put(new Employee(176, "Roshan", "IT", 600000), 60);
    employeeMap.put(new Employee(388, "Bikash", "CIVIL", 900000), 90);
    employeeMap.put(new Employee(470, "Bimal", "DEFENCE", 500000), 50);
    employeeMap.put(new Employee(624, "Sourav", "CORE", 400000), 40);
    employeeMap.put(new Employee(284, "Prakash", "SOCIAL", 1200000), 120);

    System.out.println(employeeMap);

    List<Entry<String, Integer>> entries = new ArrayList<>(map.entrySet());
    Collections.sort(entries, (o1, o2) -> o1.getKey().compareTo(o2.getKey()));

    /*
        * for (Entry<String, Integer> entry : entries) {
        * System.out.println(entry.getKey() + "   " + entry.getValue()); }
        */

    // map.entrySet().stream().sorted(Map.Entry.comparingByKey()).forEach(System.out::println);
    System.out.println("****************************");
    // map.entrySet().stream().sorted(Map.Entry.comparingByValue()).forEach(System.out::println);

    employeeMap.entrySet().stream()
            .sorted(Map.Entry.comparingByKey(Comparator.comparing(Employee::getDept).reversed()))
            .forEach(System.out::println);