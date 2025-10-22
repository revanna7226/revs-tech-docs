Prototype
=========

#. Concept:
    The Prototype Design Pattern is a creational design pattern that allows you to clone existing objects instead of creating new instances from scratch.

    It is especially useful when object creation is expensive or complex, and copying an existing object is more efficient.

#. When to Use:

   - Creating a new object is costly (e.g., initializing a large object, DB connections, or complex computations).
   - You want runtime object creation flexibility.
   - You need deep or shallow copies of objects without coupling the client to concrete classes.

#. UML Diagram

    .. code-block:: lua

        +--------------------+
        |    Prototype       |
        | + clone():Prototype|
        +--------+-----------+
                 ^
                 |
        +--------------------+
        | ConcretePrototype  |
        | + clone()          |
        +--------------------+
                 ^
                 |
               Client

#. Key Concepts
    .. list-table:: ConceptDescription
        :header-rows: 1

        * - Concept
          - Description
        * - Prototype
          - Declares the cloning interface (clone() method).
        * - ConcretePrototype
          - Implements the clone() method to duplicate itself.
        * - Client
          - Creates a new object by asking a prototype to clone itself.

Example 1 - Shallow Copy Using Cloneable:
    .. code-block:: java

        // Step 1: Prototype Class
        public class Employee implements Cloneable {
            private String name;
            private int age;

            public Employee(String name, int age) {
                this.name = name;
                this.age = age;
            }

            // Shallow clone
            @Override
            protected Object clone() throws CloneNotSupportedException {
                return super.clone();
            }

            @Override
            public String toString() {
                return "Employee{name='" + name + "', age=" + age + "}";
            }

            // Getters and Setters
            public String getName() { return name; }
            public void setName(String name) { this.name = name; }
            public int getAge() { return age; }
            public void setAge(int age) { this.age = age; }
        }

        // Step 2: Client Code
        public class PrototypeDemo {
            public static void main(String[] args) throws CloneNotSupportedException {
                Employee original = new Employee("John", 30);
                Employee clone = (Employee) original.clone();

                clone.setName("Jane"); // modifying the clone

                System.out.println(original);
                System.out.println(clone);
            }
        }

        // Output
        // Employee{name='John', age=30}
        // Employee{name='Jane', age=30}


    ✅ Shallow copy works fine for primitive fields and immutable objects.

6. Example 2: Deep Copy:
    Shallow copy only copies primitive and immutable fields, but references are shared.
    For objects with nested objects, use deep copy:

    .. code-block:: java

        class Address implements Cloneable {
            String city;

            public Address(String city) { this.city = city; }

            @Override
            protected Object clone() throws CloneNotSupportedException {
                return super.clone();
            }

            @Override
            public String toString() { return city; }
        }

        class EmployeeWithAddress implements Cloneable {
            private String name;
            private Address address;

            public EmployeeWithAddress(String name, Address address) {
                this.name = name;
                this.address = address;
            }

            @Override
            protected Object clone() throws CloneNotSupportedException {
                EmployeeWithAddress cloned = (EmployeeWithAddress) super.clone();
                cloned.address = (Address) address.clone(); // deep copy
                return cloned;
            }

            @Override
            public String toString() {
                return "Employee{name='" + name + "', address=" + address + "}";
            }
        }

        // Client Code
        public class PrototypeDeepCopyDemo {
            public static void main(String[] args) throws CloneNotSupportedException {
                Address address = new Address("New York");
                EmployeeWithAddress original = new EmployeeWithAddress("John", address);

                EmployeeWithAddress clone = (EmployeeWithAddress) original.clone();
                clone.address.city = "Los Angeles";

                System.out.println(original);
                System.out.println(clone);
            }
        }

        // Output
        // Employee{name='John', address=New York}
        // Employee{name='John', address=Los Angeles}


    ✅ Now the cloned object has its own copy of Address.
