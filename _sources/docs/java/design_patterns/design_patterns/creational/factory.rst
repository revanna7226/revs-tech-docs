Factory
=============

    The Factory Design Pattern is a Creational Design Pattern that provides an 
    interface for creating objects, but allows subclasses or dedicated methods 
    to decide which class to instantiate.

    In simpler terms — Instead of creating objects directly using new, we delegate object creation to a Factory class or method.

Real-World Analogy:
    Imagine a Document Factory that can create PDF, Word, or Excel documents. You don't 
    directly call new PDFDocument(). Instead, you ask the factory — 
    
    .. code-block:: java

        Document doc = DocumentFactory.createDocument("PDF");
    
    The factory decides which object to create.


Types of Factory Patterns:
    .. list-table::
        :header-rows: 1
        :widths: 25 75

        * - **Type**
          - **Description**
        * - **Simple Factory**
          - A single method decides which class to instantiate (not part of GoF).
        * - **Factory Method**
          - Defines an interface for creating an object, but allows subclasses to alter the type of objects created (**GoF pattern**).


Simple Factory Example
-----------------------

    .. code-block:: java

        // Step 1: Define Product Interface
        public interface Shape {
            void draw();
        }

        // Step 2: Concrete Implementations
        public class Circle implements Shape {
            @Override
            public void draw() {
                System.out.println("Drawing a Circle");
            }
        }

        public class Rectangle implements Shape {
            @Override
            public void draw() {
                System.out.println("Drawing a Rectangle");
            }
        }

        public class Square implements Shape {
            @Override
            public void draw() {
                System.out.println("Drawing a Square");
            }
        }

        // Step 3: Create Factory Class

        public class ShapeFactory {

            public static Shape getShape(String shapeType) {
                if (shapeType == null)
                    return null;

                switch (shapeType.toUpperCase()) {
                    case "CIRCLE":
                        return new Circle();
                    case "RECTANGLE":
                        return new Rectangle();
                    case "SQUARE":
                        return new Square();
                    default:
                        throw new IllegalArgumentException("Unknown shape type: " + shapeType);
                }
            }
        }

        // Step 4: Use the Factory
        public class FactoryDemo {
            public static void main(String[] args) {
                Shape circle = ShapeFactory.getShape("circle");
                circle.draw();

                Shape rectangle = ShapeFactory.getShape("rectangle");
                rectangle.draw();
            }
        }

        // Output
        Drawing a Circle
        Drawing a Rectangle

Factory Method Pattern (GoF Definition)
----------------------------------------
    In this variant, the Factory itself is abstract, and subclasses decide what to create.

Example - Notification Factory:
    .. code-block:: java

        // Step 1: Product Interface
        interface Notification {
            void notifyUser();
        }

        // Step 2: Concrete Products
        class EmailNotification implements Notification {
            @Override
            public void notifyUser() {
                System.out.println("Sending an Email Notification");
            }
        }

        class SMSNotification implements Notification {
            @Override
            public void notifyUser() {
                System.out.println("Sending an SMS Notification");
            }
        }

        // Step 4: Concrete Factories
        abstract class NotificationFactory {
            public abstract Notification createNotification();

            public void sendNotification() {
                Notification notification = createNotification();
                notification.notifyUser();
            }
        }

        // Step 4: Concrete Factories
        class EmailNotificationFactory extends NotificationFactory {
            @Override
            public Notification createNotification() {
                return new EmailNotification();
            }
        }

        class SMSNotificationFactory extends NotificationFactory {
            @Override
            public Notification createNotification() {
                return new SMSNotification();
            }
        }

        // Step 5: Client Code
        public class FactoryMethodDemo  {
            public static void main(String[] args) {
                NotificationFactory emailFactory = new EmailNotificationFactory();
                emailFactory.sendNotification();

                NotificationFactory smsFactory = new SMSNotificationFactory();
                smsFactory.sendNotification();
            }
        }

.. list-table:: **Factory Pattern Usage in Spring Framework**
   :header-rows: 1
   :widths: 35 65

   * - **Spring Concept**
     - **Pattern Used**
   * - ``BeanFactory``, ``ApplicationContext``
     - Factory Pattern
   * - ``@Bean`` methods in ``@Configuration``
     - Factory Methods
   * - ``FactoryBean<T>`` interface
     - Custom Factory for object creation
   * - ``AbstractFactoryBean``
     - Used for building framework-level beans
