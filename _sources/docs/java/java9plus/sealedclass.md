# 🧩 What is a _Sealed Class_?

A **sealed class** in Java is a class that **restricts which other classes or interfaces can extend or implement it**.

It allows you to **control the class hierarchy** — preventing unexpected subclasses while still permitting certain ones.

✅ Introduced in **Java 15 (preview)** and standardized in **Java 17**.

---

## ⚙️ Why Do We Need Sealed Classes?

Before Java 17, a class could only be:

- `final` → no subclassing allowed ❌
- `abstract` → subclassing open to anyone ✅

But sometimes we want something **in between**:

> “Only these few classes are allowed to extend me — and no others.”

That’s where **sealed classes** come in.

---

# 🚦 Sealed Class Syntax

```java
public sealed class Parent permits Child1, Child2 {
    // class body
}
```

- The keyword **`sealed`** is used before `class` or `interface`.
- The **`permits`** clause lists the allowed subclasses.
- Each permitted subclass must **explicitly declare** how it continues the hierarchy using one of:

  - `final` → cannot be extended further
  - `sealed` → can be extended further by its own permitted classes
  - `non-sealed` → removes sealing and allows open inheritance again

---

# 🧠 Example 1 — Basic Sealed Class Hierarchy

```java
public sealed class Shape permits Circle, Rectangle, Square {
    public abstract double area();
}
```

Each permitted subclass must declare how it continues the sealing:

```java
public final class Circle extends Shape {
    private double radius;
    public Circle(double radius) { this.radius = radius; }

    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}
```

```java
public non-sealed class Rectangle extends Shape {
    private double width, height;
    public Rectangle(double width, double height) {
        this.width = width; this.height = height;
    }

    @Override
    public double area() {
        return width * height;
    }
}
```

```java
public sealed class Square extends Shape permits ColoredSquare {
    private double side;
    public Square(double side) { this.side = side; }

    @Override
    public double area() {
        return side * side;
    }
}
```

```java
public final class ColoredSquare extends Square {
    private String color;
    public ColoredSquare(double side, String color) {
        super(side);
        this.color = color;
    }
}
```

✅ **Hierarchy Summary:**

- `Shape` → sealed
- `Circle` → final (no more subclassing)
- `Rectangle` → non-sealed (open again)
- `Square` → sealed again (limited to `ColoredSquare`)
- `ColoredSquare` → final (end of chain)

---

# ⚖️ Example 2 — Using a Sealed Interface

You can seal interfaces too!

```java
public sealed interface Payment permits CreditCardPayment, UpiPayment, CashPayment {
    void pay(double amount);
}

public final class CreditCardPayment implements Payment {
    public void pay(double amount) {
        System.out.println("Paid " + amount + " via Credit Card");
    }
}

public final class UpiPayment implements Payment {
    public void pay(double amount) {
        System.out.println("Paid " + amount + " via UPI");
    }
}

public non-sealed class CashPayment implements Payment {
    public void pay(double amount) {
        System.out.println("Paid " + amount + " in cash");
    }
}
```

Usage:

```java
public class Main {
    public static void main(String[] args) {
        Payment p = new CreditCardPayment();
        p.pay(250.00);
    }
}
```

Output:

```
Paid 250.0 via Credit Card
```

---

# 🧩 Example 3 — Sealed Classes and Pattern Matching

Sealed classes are great when used with **pattern matching** (`switch` or `instanceof`) since the compiler knows all possible subclasses.

```java
public sealed interface Shape permits Circle, Rectangle {}

public final class Circle implements Shape {
    double radius;
    Circle(double r) { this.radius = r; }
}

public final class Rectangle implements Shape {
    double width, height;
    Rectangle(double w, double h) { this.width = w; this.height = h; }
}
```

Now use **pattern matching** safely:

```java
public class ShapeService {
    public double area(Shape shape) {
        return switch (shape) {
            case Circle c -> Math.PI * c.radius * c.radius;
            case Rectangle r -> r.width * r.height;
        };
        // no default needed — compiler knows all possible types
    }
}
```

✅ The compiler ensures **exhaustiveness** — if you forget a subtype, you’ll get a compile-time error.

---

# 🧱 Key Rules & Constraints

| Rule                     | Description                                                 |
| ------------------------ | ----------------------------------------------------------- |
| `permits` list           | Must include all permitted subclasses explicitly            |
| Subclass requirement     | Must be in the same module (or same package if not modular) |
| Subclass declaration     | Must declare itself as `final`, `sealed`, or `non-sealed`   |
| Unlisted subclass        | Compilation error                                           |
| Sealed class inheritance | Works with `class` and `interface`                          |

---

# 🧰 Example 4 — Mixing with Records (Common Pattern)

Records are perfect for modeling **data variants** inside a sealed hierarchy.

```java
public sealed interface Vehicle permits Car, Truck, Bike { }

public record Car(String brand, String model) implements Vehicle { }
public record Truck(String brand, double loadCapacity) implements Vehicle { }
public record Bike(String brand, boolean electric) implements Vehicle { }
```

Usage:

```java
public class VehicleInfo {
    public static void main(String[] args) {
        Vehicle v = new Truck("Volvo", 8000);
        printInfo(v);
    }

    static void printInfo(Vehicle v) {
        if (v instanceof Car c) {
            System.out.println("Car: " + c.brand() + " " + c.model());
        } else if (v instanceof Truck t) {
            System.out.println("Truck: " + t.brand() + " capacity=" + t.loadCapacity());
        } else if (v instanceof Bike b) {
            System.out.println("Bike: " + b.brand() + " electric=" + b.electric());
        }
    }
}
```

---

# ✅ Advantages of Sealed Classes

| Benefit                            | Description                               |
| ---------------------------------- | ----------------------------------------- |
| **Controlled inheritance**         | Prevents unexpected subclassing           |
| **Better type safety**             | Compiler knows all possible types         |
| **Improved pattern matching**      | `switch` expressions can be exhaustive    |
| **Cleaner code**                   | Avoids unnecessary `default` in switch    |
| **Documentation & design clarity** | Shows exactly what extensions are allowed |

---

# ⚠️ Limitations

- All permitted subclasses must be **declared in the same module** (or same package if not modular).
- Subclasses must declare `final`, `sealed`, or `non-sealed`.
- Sealed classes cannot be declared **anonymous** or **local** (inside a method).
- Doesn’t replace `enum` — use `enum` for fixed values, `sealed` for fixed _types_.

---

# 🧾 Summary

| Keyword            | Meaning                                   |
| ------------------ | ----------------------------------------- |
| `sealed`           | Restricts subclassing to specific classes |
| `permits`          | Lists allowed subclasses                  |
| `final`            | Cannot be subclassed further              |
| `non-sealed`       | Removes sealing restriction               |
| `sealed interface` | Controls which classes can implement it   |

---

✅ **In short:**

> A _sealed class_ gives you the flexibility of `abstract` classes with the safety of `final` — a controlled inheritance hierarchy that the compiler and the developer both understand clearly.
