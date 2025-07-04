Factory
-------

.. admonition:: Definition

    A component responsible solely for the wholesale (not piecewise) creation of objects.

Motivation
==========
1. Object creation logic becomes too convoluted so 

2. Constructor is not descriptive
   
• Name mandated by name of containing type

• Cannot overload with same sets of arguments with different names

• Can turn into 'overloading hell'

3. Wholesale object creation (non-piecewise, unlike Builder) can
be outsourced to

• A separate function (Factory Method)

• That may exist in a separate class (Factory)

• Can create hierarchy of factories with Abstract Factory

Factory Method
==============

.. code-block:: java

    package com.revs.designpatterns.factory;

    enum CoordinateSystem {
        CARTESIAN,
        POLAR
    }

    class Point {
        private double x, y;

        protected Point(double x, double y) {
            this.x = x;
            this.y = y;
        }

        public Point(double a,
                    double b, // names do not communicate intent
                    CoordinateSystem cs) {
            switch (cs) {
                case CARTESIAN:
                    this.x = a;
                    this.y = b;
                    break;
                case POLAR:
                    this.x = a * Math.cos(b);
                    this.y = a * Math.sin(b);
                    break;
            }
        }

        // steps to add a new system
        // 1. augment CoordinateSystem
        // 2. change ctor

        // singleton field
        public static final Point ORIGIN = new Point(0, 0);

        // factory method
        public static Point newCartesianPoint(double x, double y) {
            return new Point(x, y);
        }

        public static Point newPolarPoint(double rho, double theta) {
            return new Point(rho * Math.cos(theta), rho * Math.sin(theta));
        }

        public static class Factory {
            public static Point newCartesianPoint(double x, double y) {
                return new Point(x, y);
            }
        }
    }

    class PointFactory {
        public static Point newCartesianPoint(double x, double y) {
            return new Point(x, y);
        }
    }

    class Factory {
        public static void main(String[] args) {
            Point point = new Point(2, 3, CoordinateSystem.CARTESIAN);
            Point origin = Point.ORIGIN;

            Point point1 = Point.Factory.newCartesianPoint(1, 2);
        }
    }

Abstract Factory
================

.. code-block:: java

    package com.revs.designpatterns.factory;

    import org.javatuples.Pair;
    import org.reflections.Reflections;

    import java.io.BufferedReader;
    import java.io.IOException;
    import java.io.InputStreamReader;
    import java.lang.reflect.Type;
    import java.util.*;

    interface IHotDrink {
        void consume();
    }

    class Tea implements IHotDrink {
        @Override
        public void consume() {
            System.out.println("This tea is nice but I'd prefer it with milk.");
        }
    }

    class Coffee implements IHotDrink {
        @Override
        public void consume() {
            System.out.println("This coffee is delicious");
        }
    }

    interface IHotDrinkFactory {
        IHotDrink prepare(int amount);
    }

    class TeaFactory implements IHotDrinkFactory {
        @Override
        public IHotDrink prepare(int amount) {
            System.out.println("Put in tea bag, boil water, pour " + amount + "ml, add lemon, enjoy!");
            return new Tea();
        }
    }

    class CoffeeFactory implements IHotDrinkFactory {

        @Override
        public IHotDrink prepare(int amount) {
            System.out.println("Grind some beans, boil water, pour " + amount + " ml, add cream and sugar, enjoy!");
            return new Coffee();
        }
    }

    class HotDrinkMachine {
        public enum AvailableDrink {
            COFFEE, TEA
        }

        private Map<AvailableDrink, IHotDrinkFactory> factories = new HashMap<>();

        private List<Pair<String, IHotDrinkFactory>> namedFactories = new ArrayList<>();

        public HotDrinkMachine() throws Exception {
            // option 1: use an enum
            for (AvailableDrink drink : AvailableDrink.values()) {
                String s = drink.toString();
                String factoryName = "" + Character.toUpperCase(s.charAt(0)) + s.substring(1).toLowerCase();
                Class<?> factory = Class.forName("com.revs.designpatterns.factory." + factoryName + "Factory");
                factories.put(drink, (IHotDrinkFactory) factory.getDeclaredConstructor().newInstance());
            }

            // option 2: find all implementors of IHotDrinkFactory
            Set<Class<? extends IHotDrinkFactory>> types = new Reflections("com.revs.designpatterns.factory") // ""
                    .getSubTypesOf(IHotDrinkFactory.class);
            for (Class<? extends IHotDrinkFactory> type : types) {
                namedFactories.add(new Pair<>(type.getSimpleName().replace("Factory", ""), type.getDeclaredConstructor().newInstance()));
            }
        }

        public IHotDrink makeDrink() throws IOException {
            System.out.println("Available drinks");
            for (int index = 0; index < namedFactories.size(); ++index) {
                Pair<String, IHotDrinkFactory> item = namedFactories.get(index);
                System.out.println("" + index + ": " + item.getValue0());
            }

            BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
            while (true) {
                String s;
                int i, amount;
                if ((s = reader.readLine()) != null && (i = Integer.parseInt(s)) >= 0 && i < namedFactories.size()) {
                    System.out.println("Specify amount: ");
                    s = reader.readLine();
                    if (s != null && (amount = Integer.parseInt(s)) > 0) {
                        return namedFactories.get(i).getValue1().prepare(amount);
                    }
                }
                System.out.println("Incorrect input, try again.");
            }
        }

        public IHotDrink makeDrink(AvailableDrink drink, int amount) {
            return ((IHotDrinkFactory) factories.get(drink)).prepare(amount);
        }
    }

    class AbstractFactory {
        public static void main(String[] args) throws Exception {
            HotDrinkMachine machine = new HotDrinkMachine();
            IHotDrink tea = machine.makeDrink(HotDrinkMachine.AvailableDrink.TEA, 200);
            tea.consume();

            // interactive
            IHotDrink drink = machine.makeDrink();
            drink.consume();
        }
    }

Excercise
=========

Factory Coding Exercise
You are given a class called Person . The person has two fields: id , and name .

Please implement a non-static PersonFactory that has a createPerson()  method that 
takes a person's name and returns a new instance of Person .

The id  of the person returned should be set as a 0-based index of the object created by that factory. 
So, the first person the factory makes should have id=0, second id=1 and so on.

Solution
=========

.. code-block:: java

    package com.activemesa.creational.factories.exercise;

    class Person {
        public int id;
        public String name;

        public Person(int id, String name) {
            this.id = id;
            this.name = name;
        }
    }

    class PersonFactory {
        private int id = 0;

        public Person createPerson(String name) {
            return new Person(id++, name);
        }
    }

Tests
=====

.. code-block:: java

    package com.activemesa.creational.factories.exercise;

    import org.junit.Test;

    import static org.junit.Assert.assertEquals;

    public class Evaluate {
        @Test
        public void test() {
            PersonFactory pf = new PersonFactory();

            Person p1 = pf.createPerson("Chris");
            assertEquals("Chris", p1.name);
            assertEquals(0, p1.id);

            Person p2 = pf.createPerson("Sarah");
            assertEquals(1, p2.id);
        }
    }

Summary
=======
- A factory method is a static method that creates objects

- A factory can take care of object creation

- A factory can be external or reside inside the object as an inner class 

- Hierarchies of factories can be used to create related objects