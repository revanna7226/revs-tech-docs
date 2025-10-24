Bridge Design Pattern — Detailed Explanation with Java Example
===============================================================

Overview
--------
The Bridge pattern is a structural design pattern that decouples an abstraction from
its implementation so the two can vary independently. It is useful when both the
abstraction and its implementation may need to change over time — by separating them
you avoid a combinatorial explosion of subclasses.

Intent
------
- Separate an abstraction from its implementation.
- Allow the abstraction and implementation to evolve independently.
- Reduce compile-time coupling between abstraction and concrete implementations.

Key Participants
----------------
- Abstraction
  - Defines the abstraction's interface and maintains a reference to an Implementor.
- RefinedAbstraction
  - Extends the Abstraction and uses the Implementor for concrete operations.
- Implementor
  - Defines the interface for implementation classes. This interface doesn't need to mirror Abstraction's interface exactly — they can be different, as long as Abstraction uses Implementor for work.
- ConcreteImplementor
  - Implements the Implementor interface.

UML (simplified ASCII)
----------------------
 Abstraction<>-----Implementor
     ^                  ^
     |                  |
RefinedAbstraction   ConcreteImplementorA
                     ConcreteImplementorB

When to use
-----------
- When you want to avoid a permanent binding between an abstraction and its implementation.
- When both the abstractions and their implementations should be extensible by subclassing.
- When changes in the implementation should not affect clients of the abstraction.

Advantages
----------
- Improves flexibility and extensibility.
- Reduces subclass explosion (avoids creating subclasses for every combination).
- Implementation can be changed at runtime.

Trade-offs
----------
- More complex design because of extra indirection.
- Slight increase in number of classes.

Java Example: Shapes + Colors
----------------------------
We model a simple scenario: different shapes (Circle, Square) can be drawn using
different colors (Red, Green). Shape is the Abstraction, Color is the Implementor.

Files (all in the same package or default package):
- Color.java (Implementor)
- RedColor.java (ConcreteImplementor)
- GreenColor.java (ConcreteImplementor)
- Shape.java (Abstraction)
- Circle.java (RefinedAbstraction)
- Square.java (RefinedAbstraction)
- BridgeDemo.java (Client / Main)

Code
----
.. code-block:: java

    // Color.java
    // Implementor
    public interface Color {
        // Apply color to some shape-specific description (could be drawing logic)
        void applyColor();
    }

.. code-block:: java

    // RedColor.java
    // ConcreteImplementor
    public class RedColor implements Color {
        @Override
        public void applyColor() {
            System.out.println("Applying red color");
        }
    }

.. code-block:: java

    // GreenColor.java
    // ConcreteImplementor
    public class GreenColor implements Color {
        @Override
        public void applyColor() {
            System.out.println("Applying green color");
        }
    }

.. code-block:: java

    // Shape.java
    // Abstraction
    public abstract class Shape {
        // Bridge to implementation
        protected Color color;

        protected Shape(Color color) {
            this.color = color;
        }

        // Clients call this; the implementation is delegated to Color
        public abstract void draw();

        // Allow changing implementation at runtime
        public void setColor(Color color) {
            this.color = color;
        }
    }

.. code-block:: java

    // Circle.java
    // RefinedAbstraction
    public class Circle extends Shape {
        private int radius;

        public Circle(Color color, int radius) {
            super(color);
            this.radius = radius;
        }

        @Override
        public void draw() {
            System.out.print("Drawing Circle of radius " + radius + " - ");
            color.applyColor();
        }
    }

.. code-block:: java

    // Square.java
    // RefinedAbstraction
    public class Square extends Shape {
        private int side;

        public Square(Color color, int side) {
            super(color);
            this.side = side;
        }

        @Override
        public void draw() {
            System.out.print("Drawing Square with side " + side + " - ");
            color.applyColor();
        }
    }

.. code-block:: java

    // BridgeDemo.java
    // Client code
    public class BridgeDemo {
        public static void main(String[] args) {
            Color red = new RedColor();
            Color green = new GreenColor();

            Shape circle = new Circle(red, 5);
            Shape square = new Square(green, 10);

            circle.draw();
            square.draw();

            // Change implementation at runtime
            square.setColor(red);
            square.draw();
        }
    }

Compile & Run
-------------
1. Save each class in its own .java file with the exact class name (e.g., Circle.java).
2. Compile:
   javac *.java
3. Run:
   java BridgeDemo

Expected Output
---------------
Drawing Circle of radius 5 - Applying red color
Drawing Square with side 10 - Applying green color
Drawing Square with side 10 - Applying red color

How this demonstrates Bridge
-----------------------------
- Shape (Abstraction) does not know details about how color is applied; it delegates to
  the Color implementor.
- Circle and Square can be extended without touching color implementations.
- RedColor and GreenColor can be changed, extended or replaced independently of shapes.
- At runtime you can change the Color instance inside a Shape (setColor), showing the
  independent variation of abstraction and implementation.

Real-world analogies
--------------------
- Remote (abstraction) and Device (implementation). You can have different remotes for
  TVs, Radios, etc., but the same remote abstraction can work with different devices
  (the implementation), and new remotes or devices can be added freely.
- GUI widget hierarchy (abstraction) and platform-specific drawing APIs (implementation).

Summary
-------
The Bridge pattern separates abstraction from implementation, promoting independent
extensibility and reducing coupling. Use it when you expect both sides to change
independently or when you need to switch implementations at runtime without
affecting client code.