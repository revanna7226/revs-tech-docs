Functional Interfaces in Java
=============================

What is a Functional Interface?
-------------------------------

A **Functional Interface** in Java is an interface that has **exactly one abstract method** (SAM = *Single Abstract Method*).

* Introduced in **Java 8** to support **Lambda Expressions** and **Method References**.
* Can still have any number of **default methods** and **static methods**.
* Annotated with ``@FunctionalInterface`` (optional but recommended).

Why Functional Interfaces?
---------------------------

* Enable **lambda expressions** and **method references**
* Reduce boilerplate code
* Improve readability
* Encourage functional programming style in Java

Examples of Functional Interfaces
---------------------------------

Java provides many built-in ones in the ``java.util.function`` package:

* ``Predicate<T> - test()`` – test condition returning boolean
* ``Function<T, R> - apply()`` – takes input ``T``, returns output ``R``
* ``Consumer<T> - accept()`` – takes input ``T``, returns nothing
* ``Supplier<T> - get()`` – returns ``T`` (no input)

You can also **create your own**.

Creating a Functional Interface
-------------------------------

.. code-block:: java

    @FunctionalInterface
    interface MyFunctionalInterface {
        void greet(String name);

        // You can still add default/static methods
        default void sayHello() {
            System.out.println("Hello from default method!");
        }

        static void sayHi() {
            System.out.println("Hi from static method!");
        }
    }

Using Functional Interface with Lambda
--------------------------------------

.. code-block:: java

    public class FunctionalInterfaceDemo {
        public static void main(String[] args) {
            // Using lambda expression
            MyFunctionalInterface greeter = name -> System.out.println("Hello, " + name);

            greeter.greet("Revs");   // Hello, Revs
            greeter.sayHello();        // Hello from default method!
            MyFunctionalInterface.sayHi(); // Hi from static method!
        }
    }

Built-in Functional Interfaces (Examples)
-----------------------------------------

Predicate
^^^^^^^^^

Checks a condition and returns boolean.

.. code-block:: java

    import java.util.function.Predicate;

    public class PredicateDemo {
        public static void main(String[] args) {
            Predicate<String> startsWithR = str -> str.startsWith("R");

            System.out.println(startsWithR.test("Revs"));  // true
            System.out.println(startsWithR.test("Java"));  // false
        }
    }

Function
^^^^^^^^

Takes input and produces output.

.. code-block:: java

    import java.util.function.Function;

    public class FunctionDemo {
        public static void main(String[] args) {
            Function<String, Integer> lengthFunc = str -> str.length();

            System.out.println(lengthFunc.apply("Java"));  // 4
        }
    }

Consumer
^^^^^^^^

Consumes input, returns nothing.

.. code-block:: java

    import java.util.function.Consumer;

    public class ConsumerDemo {
        public static void main(String[] args) {
            Consumer<String> printer = str -> System.out.println("Printing: " + str);

            printer.accept("Hello World"); // Printing: Hello World
        }
    }

Supplier
^^^^^^^^

Supplies values without input.

.. code-block:: java

    import java.util.function.Supplier;

    public class SupplierDemo {
        public static void main(String[] args) {
            Supplier<Double> randomValue = () -> Math.random();

            System.out.println(randomValue.get()); // e.g. 0.8231
        }
    }

.. admonition:: Note
    :class: note
    
    Apart from ``Consumer``, ``Predicate``, ``Supplier`` and ``Function``, Java provides many other **functional interfaces**. Some are older (like ``Runnable`` and ``Callable``), and many are available in the ``java.util.function`` package.

Runnable
^^^^^^^^^

Represents a task that can be executed without input and without returning a result.

.. code-block:: java

    public class RunnableDemo {
        public static void main(String[] args) {
            Runnable task = () -> System.out.println("Running a task!");

            Thread thread = new Thread(task);
            thread.start(); // Running a task!
        }
    }

Callable
^^^^^^^^^

It is similar to ``Runnable`` but with some key differences:

* ``Callable`` can **return a result**.
* ``Callable`` can **throw checked exceptions**.
* Designed to work with ``ExecutorService`` for concurrent task execution.

Defined in ``java.util.concurrent``.

.. code-block:: java

    import java.util.concurrent.Callable;

    public class CallableDemo {
        public static void main(String[] args) throws Exception {
            Callable<Integer> task = () -> 42;

            System.out.println(task.call()); // 42
        }
    }

BiFunction
^^^^^^^^^^^^^

Takes **two inputs** and produces a result.

.. code-block:: java

    import java.util.function.BiFunction;

    public class BiFunctionDemo {
        public static void main(String[] args) {
            BiFunction<Integer, Integer, Integer> adder = (a, b) -> a + b;

            System.out.println(adder.apply(5, 10)); // 15
        }
    }

BiConsumer
^^^^^^^^^^^^^

Takes **two inputs** and returns nothing.

.. code-block:: java

    import java.util.function.BiConsumer;

    public class BiConsumerDemo {
        public static void main(String[] args) {
            BiConsumer<String, Integer> printer =
                (name, age) -> System.out.println(name + " is " + age + " years old.");

            printer.accept("Revs", 30); // Revs is 30 years old.

            // Example with Map
            Map<String, Integer> map=new HashMap<>();
            map.put("basant",5000);
            map.put("santosh",15000);
            map.put("javed",12000);

            map.forEach((k,v)-> System.out.println(k+","+v));
        }
    }

UnaryOperator
^^^^^^^^^^^^^

A specialization of ``Function<T, R>`` where input and output are the **same type**.

.. code-block:: java

    import java.util.function.UnaryOperator;

    public class UnaryOperatorDemo {
        public static void main(String[] args) {
            UnaryOperator<String> toUpper = str -> str.toUpperCase();

            System.out.println(toUpper.apply("java")); // JAVA
        }
    }

BinaryOperator
^^^^^^^^^^^^^^

A specialization of ``BiFunction<T, T, T>`` where inputs and output are the **same type**.

.. code-block:: java

    import java.util.function.BinaryOperator;

    public class BinaryOperatorDemo {
        public static void main(String[] args) {
            BinaryOperator<Integer> multiply = (a, b) -> a * b;

            System.out.println(multiply.apply(5, 4)); // 20
        }
    }

BiPredicate
^^^^^^^^^^^^^^

Takes **two inputs** and returns a boolean.

.. code-block:: java

    BiPredicate<String,String> biPredicate=new BiPredicate<String, String>() {
        @Override
        public boolean test(String s1, String s2) {
            return s1.equals(s2);
        }
    };
    System.out.println(biPredicate.test("madam","madam"));


    BiPredicate<String,String> equalsPredicate= ( s1,  s2) ->s1.equals(s2);
    BiPredicate<String,String> lengthPredicate=(s1,s2)->s1.length()==s2.length();

    boolean output=lengthPredicate.and(equalsPredicate).test("madam","madam");
    System.out.println("output : "+output);

    boolean orOutput=lengthPredicate.or(equalsPredicate).test("abc","def");
    System.out.println("orOutput : "+orOutput);

    System.out.println(equalsPredicate.test("madam","madam"));

Comparator
^^^^^^^^^^^^^

Represents a comparison function that compares two objects.

.. code-block:: java

    import java.util.Comparator;
    import java.util.Arrays;

    public class ComparatorDemo {
        public static void main(String[] args) {
            String[] names = {"Zara", "Revs", "Anjali"};

            Arrays.sort(names, (a, b) -> a.compareTo(b));

            System.out.println(Arrays.toString(names)); // [Anjali, Revs, Zara]
        }
    }    