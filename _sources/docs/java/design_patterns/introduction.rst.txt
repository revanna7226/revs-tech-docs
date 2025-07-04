Introduction
=============

Solid Design Principles
-----------------------

Useful principles of object-oriented design.

Design principles introduced by Robert C Martin.

Reference: https://www.geeksforgeeks.org/solid-principle-in-programming-understand-with-real-life-examples/

- Design patterns are common architectural approaches
- Popularized by the Gang of Four book (1994)
  Smalltalk & C++
- Translated to many OOP languages C#, Java, C++, ...
- so Universally relevant

  - Internalized in some programming languages
  - Libraries
  - Your own code!

.. toctree::
  :maxdepth: 4
  
  solid_principles/srp
  solid_principles/ocp
  solid_principles/lsp
  solid_principles/isp
  solid_principles/dip

Summery
````````

- Single Responsibility Principle
  - A class should only have one reason to change
  - Separation of concerns - different classes handling different, independent tasks/problems
- Open-Closed Principle
  - Classes should be open for extension but closed for modification
- Liskov Substition Principle
  - You should be able to substitute a base type for a subtype
- Interface Segregation Principle
  - Don't put too much into an interface; split into separate interfaces
  - YAGNI - You Ain't Going to Need It
- Dependency Inversion Principle
  - High-level modules should not depend upon low-level ones; use abstractions

Gamma Categorization
====================
Design Patterns are typically split into three categories
This is called Gamma Categorization after Erich Gamma, one of GoF authors


1. Creational Patterns
----------------------

- Deal with the creation (construction) of objects
- Explicit (constructor) vs. implicit (DI, reflection, etc.)
- Wholesale (single statement) vs. piecewise (step-by-step)

.. admonition:: Examples

    - Builder
    - Factories
        - Abstract Factory
        - Factory Method
    - Prototype
    - Singleton


2. Structural Patterns
----------------------

- Concerned with the structure (e.g., class members)
- Many patterns are wrappers that mimic the underlying class' interface
- Stress the importance of good API design

.. admonition:: Examples

    - Adapter
    - Bridge
    - Composite
    - Decorator
    - Façade
    - Flyweight
    - Proxy


3. Behavioral Patterns
----------------------

They are all different; no central theme

.. admonition:: Examples

    - Chain of Responsibility
    - Command
    - Interpreter
    - Iterator
    - Mediator
    - Memento
    - Null Object
    - Observer
    - State
    - Strategy
    - Template Method
    - Visitor