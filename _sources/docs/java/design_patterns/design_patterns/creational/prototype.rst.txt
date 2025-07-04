Prototype
=========

Motivation
----------

1. Complicated objects (e.g., cars) aren't designed from scratch
   
2. They reiterate existing designs
An existing (partially or fully constructed) design is a
Prototype

3. We make a copy (clone) the prototype and customize it

4. Requires 'deep copy' support
We make the cloning convenient (e.g., via a Factory)

.. admonition:: Definition

    A partially or fully initialized object that you copy (clone) and make use of.

Don't Use Cloneable
-------------------

.. code-block:: java

    package com.revs.designpatterns.prototype;

    import java.util.Arrays;

    // Cloneable is a marker interface
    class Address implements Cloneable {
        public String streetName;
        public int houseNumber;

        public Address(String streetName, int houseNumber) {
            this.streetName = streetName;
            this.houseNumber = houseNumber;
        }

        @Override
        public String toString() {
            return "Address{" +
                    "streetName='" + streetName + '\'' +
                    ", houseNumber=" + houseNumber +
                    '}';
        }

        // base class clone() is protected
        @Override
        public Object clone() throws CloneNotSupportedException {
            return new Address(streetName, houseNumber);
        }
    }

    class Person implements Cloneable {
        public String[] names;
        public Address address;

        public Person(String[] names, Address address) {
            this.names = names;
            this.address = address;
        }

        @Override
        public String toString() {
            return "Person{" +
                    "names=" + Arrays.toString(names) +
                    ", address=" + address +
                    '}';
        }

        @Override
        public Object clone() throws CloneNotSupportedException {
            return new Person(
                    // clone() creates a shallow copy!
                    /*names */ names.clone(),

                    // fixes address but not names
                    /*address */ // (Address) address.clone()
                    address instanceof Cloneable ? (Address) address.clone() : address
            );
        }
    }

    class CloneableDemo {
        public static void main(String[] args)
                throws CloneNotSupportedException {
            Person john = new Person(new String[]{"John", "Smith"},
                    new Address("London Road", 123));

            // shallow copy, not good:
            //Person jane = john;

            // jane is the girl next door
            Person jane = (Person) john.clone();
            jane.names[0] = "Jane"; // clone is (originally) shallow copy
            jane.address.houseNumber = 124; // oops, also changed john

            System.out.println(john);
            System.out.println(jane);
        }
    }

Copy Constructors
-----------------

.. code-block:: java

    package com.revs.designpatterns.prototype;

    class Address1 {
        public String streetAddress, city, country;

        public Address1(String streetAddress, String city, String country) {
            this.streetAddress = streetAddress;
            this.city = city;
            this.country = country;
        }

        public Address1(Address1 other) {
            this(other.streetAddress, other.city, other.country);
        }

        @Override
        public String toString() {
            return "Address{" +
                    "streetAddress='" + streetAddress + '\'' +
                    ", city='" + city + '\'' +
                    ", country='" + country + '\'' +
                    '}';
        }
    }

    class Employee {
        public String name;
        public Address1 address;

        public Employee(String name, Address1 address) {
            this.name = name;
            this.address = address;
        }

        public Employee(Employee other) {
            name = other.name;
            address = new Address1(other.address);
        }

        @Override
        public String toString() {
            return "Employee{" +
                    "name='" + name + '\'' +
                    ", address=" + address +
                    '}';
        }
    }

    class CopyConstructorDemo {
        public static void main(String[] args) {
            Employee john = new Employee("John",
                    new Address1("123 London Road", "London", "UK"));

            //Employee chris = john;
            Employee chris = new Employee(john);

            chris.name = "Chris";
            System.out.println(john);
            System.out.println(chris);
        }
    }

Copy Through Serialization
--------------------------

.. code-block:: java

    package com.revs.designpatterns.prototype;

    import org.apache.commons.lang3.SerializationUtils;

    import java.io.Serializable;

    // some libraries use reflection (no need for Serializable)
    class Foo implements Serializable {
        public int stuff;
        public String whatever;

        public Foo(int stuff, String whatever) {
            this.stuff = stuff;
            this.whatever = whatever;
        }

        @Override
        public String toString() {
            return "Foo{" +
                    "stuff=" + stuff +
                    ", whatever='" + whatever + '\'' +
                    '}';
        }
    }

    class CopyThroughSerialize {
        public static void main(String[] args) {
            Foo foo = new Foo(42, "life");
            // use apache commons!
            Foo foo2 = SerializationUtils.roundtrip(foo);

            foo2.whatever = "xyz";

            System.out.println(foo);
            System.out.println(foo2);
        }
    }


Exercise
--------

Given the following class definitions, you are asked to implement Line.deepCopy()  to perform a deep copy of the current Line  object.

Solution
--------

.. code-block:: java

    package com.revs.designpatterns.prototype;

    class Point {
        public int x, y;

        public Point(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }

    class Line {
        public Point start, end;

        public Line(Point start, Point end) {
            this.start = start;
            this.end = end;
        }

        public Line deepCopy() {
            Point newStart = new Point(start.x, start.y);
            Point newEnd = new Point(end.x, end.y);
            return new Line(newStart, newEnd);
        }
    }

Tests
-----

.. code-block:: java

    package com.revs.designpatterns.prototype;

    import org.junit.Test;
    import static org.junit.Assert.assertEquals;

    public class Solution
    {
        @Test
        public void test()
        {
            Line line1 = new Line(
                    new Point(3, 3),
                    new Point(10, 10)
            );

            Line line2 = line1.deepCopy();
            line1.start.x = line1.end.x = line1.start.y = line1.end.y = 0;

            assertEquals(3, line2.start.x);
            assertEquals(3, line2.start.y);
            assertEquals(10, line2.end.x);
            assertEquals(10, line2.end.y);
        }
    }

Summary
-------

To implement a prototype, partially construct an object and store it somewhere 

Clone the prototype

- Implement your own deep copy functionality; or
- Serialize and deserialize
  
Customize the resulting instance


