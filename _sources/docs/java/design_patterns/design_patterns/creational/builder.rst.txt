Builder
=======

Builder Pattern
---------------

Overview:
    - This is a ``creational design pattern`` that is used to construct complex objects step-by-step.
    - It allows you to produce different representations of an object using the same construction process.
    - To separate the construction of a complex object from its representation, so the same construction process can create different representations.

When construction gets a little bit too complicated.

.. admonition:: Definition

    When piece-wise object construction is complicated, provide an API for doing it succinctly.

Problem:
    When an object requires many parameters — especially optional ones — using constructors or telescoping constructors becomes unreadable:

Solution:
    The Builder pattern solves this problem by:

    - Separating object creation from its representation.
    - Providing a step-by-step construction process.
    - Making the code more readable, flexible, and maintainable.

**Example 1 - Basic Builder Pattern:**

    .. code-block:: java

        // Step 1: Create the Product class
        public class User {
            // Required parameters
            private String firstName;
            private String lastName;

            // Optional parameters
            private int age;
            private String city;
            private String occupation;
            private boolean isEmployed;

            // Private constructor
            private User(UserBuilder builder) {
                this.firstName = builder.firstName;
                this.lastName = builder.lastName;
                this.age = builder.age;
                this.city = builder.city;
                this.occupation = builder.occupation;
                this.isEmployed = builder.isEmployed;
            }

            @Override
            public String toString() {
                return "User [firstName=" + firstName + ", lastName=" + lastName +
                        ", age=" + age + ", city=" + city + ", occupation=" + occupation +
                        ", employed=" + isEmployed + "]";
            }

            // Static Builder Class
            public static class UserBuilder {
                private final String firstName;
                private final String lastName;
                private int age;
                private String city;
                private String occupation;
                private boolean isEmployed;

                public UserBuilder(String firstName, String lastName) {
                    this.firstName = firstName;
                    this.lastName = lastName;
                }

                public UserBuilder age(int age) {
                    this.age = age;
                    return this;
                }

                public UserBuilder city(String city) {
                    this.city = city;
                    return this;
                }

                public UserBuilder occupation(String occupation) {
                    this.occupation = occupation;
                    return this;
                }

                public UserBuilder isEmployed(boolean isEmployed) {
                    this.isEmployed = isEmployed;
                    return this;
                }

                public User build() {
                    return new User(this);
                }
            }
        }

        // Step 2: Use the Builder Pattern
        public class BuilderPatternDemo {
            public static void main(String[] args) {
                User user1 = new User.UserBuilder("John", "Doe")
                        .age(30)
                        .city("New York")
                        .occupation("Engineer")
                        .isEmployed(true)
                        .build();

                User user2 = new User.UserBuilder("Jane", "Smith")
                        .city("San Francisco")
                        .build();

                System.out.println(user1);
                System.out.println(user2);
            }
        }

        // Output:
        // User [firstName=John, lastName=Doe, age=30, city=New York, occupation=Engineer, employed=true]
        // User [firstName=Jane, lastName=Smith, age=0, city=San Francisco, occupation=null, employed=false]

**Example 2 - Builder Pattern with Director:**

    In more structured systems (like frameworks), a Director class can control the building process.

    .. code-block:: java

        // Product
        class House {
            private String walls;
            private String roof;
            private String floor;

            public void setWalls(String walls) { this.walls = walls; }
            public void setRoof(String roof) { this.roof = roof; }
            public void setFloor(String floor) { this.floor = floor; }

            @Override
            public String toString() {
                return "House [walls=" + walls + ", roof=" + roof + ", floor=" + floor + "]";
            }
        }

        // Builder Interface
        interface HouseBuilder {
            void buildWalls();
            void buildRoof();
            void buildFloor();
            House getHouse();
        }

        // Concrete Builder
        class WoodenHouseBuilder implements HouseBuilder {
            private House house = new House();

            public void buildWalls() { house.setWalls("Wooden Walls"); }
            public void buildRoof() { house.setRoof("Wooden Roof"); }
            public void buildFloor() { house.setFloor("Wooden Floor"); }

            public House getHouse() { return house; }
        }

        // Director
        class CivilEngineer {
            private HouseBuilder houseBuilder;

            public CivilEngineer(HouseBuilder houseBuilder) {
                this.houseBuilder = houseBuilder;
            }

            public House constructHouse() {
                houseBuilder.buildWalls();
                houseBuilder.buildRoof();
                houseBuilder.buildFloor();
                return houseBuilder.getHouse();
            }
        }

        // Client
        public class BuilderWithDirectorDemo {
            public static void main(String[] args) {
                HouseBuilder woodenBuilder = new WoodenHouseBuilder();
                CivilEngineer engineer = new CivilEngineer(woodenBuilder);

                House house = engineer.constructHouse();
                System.out.println(house);
            }
        }

        // Output:
        // House [walls=Wooden Walls, roof=Wooden Roof, floor=Wooden Floor]

Fluent Builder Inheritance with Recursive Generics:
---------------------------------------------------

Sometimes Builder is inherit from another Builder.

.. code-block:: java

    package com.revs.designpatterns;

    // builder inheritance with recursive generics
    class Person {
        public String name;

        public String position;

        @Override
        public String toString() {
            return "Person{" +
                    "name='" + name + '\'' +
                    ", position='" + position + '\'' +
                    '}';
        }
    }

    class PersonBuilder<SELF extends PersonBuilder<SELF>> {
        protected Person person = new Person();

        // critical to return SELF here
        public SELF withName(String name) {
            person.name = name;
            return self();
        }

        protected SELF self() {
            // unchecked cast, but actually safe
            // proof: try sticking a non-PersonBuilder
            //        as SELF parameter; it won't work!
            return (SELF) this;
        }

        public Person build() {
            return person;
        }
    }

    class EmployeeBuilder
            extends PersonBuilder<EmployeeBuilder> {
        public EmployeeBuilder worksAs(String position) {
            person.position = position;
            return self();
        }

        @Override
        protected EmployeeBuilder self() {
            return this;
        }
    }

    class BuilderRecursiveGenerics {
        public static void main(String[] args) {
            EmployeeBuilder eb = new EmployeeBuilder()
                    .withName("Dmitri")
                    .worksAs("Quantitative Analyst");
            System.out.println(eb.build());
        }
    }


Faceted Builder
---------------

Intent:
    To separate the construction of different aspects (facets) of a complex object into multiple coordinated builders — all sharing the same underlying object instance.

Example Scenario:
    Let's build a complex Person object that has two major facets:
    - Address Information (where they live)
    - Job Information (where they work)

    Instead of cramming everything into a single Builder class, we create different builders for each facet, but they all operate on the same underlying Person object.    

.. code-block:: java

    package com.revs.designpatterns.builder;

    class Person {
        // address
        public String streetAddress, postcode, city;

        // employment
        public String companyName, position;
        public int annualIncome;

        @Override
        public String toString() {
            return "Person{" +
                    "streetAddress='" + streetAddress + '\'' +
                    ", postcode='" + postcode + '\'' +
                    ", city='" + city + '\'' +
                    ", companyName='" + companyName + '\'' +
                    ", position='" + position + '\'' +
                    ", annualIncome=" + annualIncome +
                    '}';
        }
    }

    // builder facade
    class PersonBuilder {
        // the object we're going to build
        protected Person person = new Person(); // reference!

        public PersonJobBuilder works() {
            return new PersonJobBuilder(person);
        }

        public PersonAddressBuilder lives() {
            return new PersonAddressBuilder(person);
        }

        public Person build() {
            return person;
        }
    }

    class PersonAddressBuilder extends PersonBuilder {
        public PersonAddressBuilder(Person person) {
            this.person = person;
        }

        public PersonAddressBuilder at(String streetAddress) {
            person.streetAddress = streetAddress;
            return this;
        }

        public PersonAddressBuilder withPostcode(String postcode) {
            person.postcode = postcode;
            return this;
        }

        public PersonAddressBuilder in(String city) {
            person.city = city;
            return this;
        }
    }

    class PersonJobBuilder extends PersonBuilder {
        public PersonJobBuilder(Person person) {
            this.person = person;
        }

        public PersonJobBuilder at(String companyName) {
            person.companyName = companyName;
            return this;
        }

        public PersonJobBuilder asA(String position) {
            person.position = position;
            return this;
        }

        public PersonJobBuilder earning(int annualIncome) {
            person.annualIncome = annualIncome;
            return this;
        }
    }

    class FacetedBuilder {
        public static void main(String[] args) {
            PersonBuilder pb = new PersonBuilder();
            Person person = pb
                    .lives()
                    .at("123 London Road")
                    .in("London")
                    .withPostcode("SW12BC")
                    .works()
                    .at("Fabrikam")
                    .asA("Engineer")
                    .earning(123000)
                    .build();
            System.out.println(person);
        }
    }