# What is a `record`?

A **record** is a special kind of class introduced to Java to act as a **concise, immutable data carrier**. It saves you from writing boilerplate for:

- private final fields,
- constructor,
- accessors,
- `equals()` / `hashCode()`,
- `toString()`.

Records were previewed in Java 14–15 and standardized in Java 16. A record implicitly extends `java.lang.Record`.

---

# Key facts & rules (short)

- Syntax: `record Name(componentType componentName, ...) { ... }`.
- Each component becomes a private final field and has an accessor method with the same name.
- The compiler auto-generates:

  - a canonical constructor,
  - accessor methods,
  - `equals()`, `hashCode()`, and `toString()`.

- Records **cannot** extend other classes (they implicitly extend `Record`).
- Records **are effectively final** for inheritance (you cannot subclass a record).
- You **can** add static fields, static methods, instance methods, additional constructors, implement interfaces, and override the generated methods if needed.
- Resources: components must be explicitly assigned in any explicit canonical constructor.
- Use records when you need a simple, immutable DTO/value object.

---

# 1) Basic record — minimal form

```java
public record Person(String name, int age) { }
```

What this gives you:

- private final `String name; private final int age;`
- public `Person(String name, int age)` (canonical constructor)
- `public String name()`, `public int age()` accessors
- `equals`, `hashCode`, `toString` based on `name` and `age`

Usage:

```java
public class Main {
    public static void main(String[] args) {
        Person p = new Person("Alice", 30);
        System.out.println(p.name());            // Alice
        System.out.println(p.age());             // 30
        System.out.println(p);                   // Person[name=Alice, age=30]
    }
}
```

---

# 2) Compact canonical constructor (validation)

You can validate constructor parameters using the **compact form** — you omit parameter list and can reference components directly:

```java
public record Person(String name, int age) {
    // compact constructor
    public Person {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("name must not be blank");
        }
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("age out of range");
        }
        // implicit assignment to this.name = name; this.age = age; happens automatically
    }
}
```

Notes:

- In the compact canonical constructor you do **not** reassign fields — the compiler will assign the validated parameters to the record components automatically if you don't shadow or reassign them.
- If you write a full canonical constructor signature (`public Person(String name, int age) { ... }`) you _must_ assign `this.name = name; this.age = age;` (compiler enforces).

Example usage:

```java
Person p = new Person("Bob", 25); // OK
Person p2 = new Person("", 25);   // throws IllegalArgumentException
```

---

# 3) Full canonical constructor (explicit assignments)

```java
public record Point(int x, int y) {
    public Point {
        // compact is fine, but here's the full form:
    }

    // full form:
    // public Point(int x, int y) {
    //     this.x = x; // required in full form
    //     this.y = y;
    // }
}
```

If you use the full form with parameter list, you must set the component fields yourself.

---

# 4) Add methods, static fields, and implement interfaces

Records are more than data carriers — you can add behavior:

```java
public record Rectangle(double width, double height) implements Comparable<Rectangle> {

    public double area() {
        return width * height;
    }

    public double perimeter() {
        return 2 * (width + height);
    }

    // implement Comparable
    @Override
    public int compareTo(Rectangle other) {
        return Double.compare(this.area(), other.area());
    }

    // static factory
    public static Rectangle square(double side) {
        return new Rectangle(side, side);
    }
}
```

Usage:

```java
Rectangle r = Rectangle.square(5.0);
System.out.println(r.area()); // 25.0
```

---

# 5) Overriding accessor or `toString` or `equals` / `hashCode`

You may override any generated method (accessor, `toString`, etc.):

```java
public record Person(String name, int age) {
    // override accessor to always return trimmed name
    public String name() {
        return name == null ? null : name.trim();
    }

    // custom toString
    @Override
    public String toString() {
        return "Person{name=%s, age=%d}".formatted(name(), age());
    }
}
```

Caution: If you override accessor method you must still ensure the canonical constructor initializes the backing field correctly.

---

# 6) "With" style copy (records are immutable — to modify, create a new instance)

Records are immutable; to create a modified copy, create a helper:

```java
public record Person(String name, int age) {
    public Person withName(String name) {
        return new Person(name, this.age);
    }

    public Person withAge(int age) {
        return new Person(this.name, age);
    }
}
```

Usage:

```java
Person p1 = new Person("Alice", 30);
Person p2 = p1.withAge(31); // new record, p1 unchanged
```

---

# 7) Generics, nested records, and arrays

Records support generics and can be nested:

```java
public record Pair<K, V>(K key, V value) { }

public class Example {
    public static void main(String[] args) {
        Pair<String, Integer> p = new Pair<>("one", 1);
        System.out.println(p); // Pair[key=one, value=1]
    }
}
```

---

# 8) Serialization note

Records can implement `Serializable`. If you rely on serialization, be mindful of adding/renaming components (compatibility). The automatic `equals`/`hashCode`/`toString` rely on components.

---

# 9) Limitations & gotchas

- Records are intended for simple, shallow, immutable data carriers — if your type has significant mutable state or behavior, a class may be more appropriate.
- Records cannot extend another class (implicitly extend `java.lang.Record`).
- Records are best for value objects (DTOs, keys, tuples).
- Avoid returning mutable internal objects (e.g., a `List` field) directly from accessors — that would expose mutability. Instead return immutable copies or unmodifiable views.

Example pitfall:

```java
public record Data(List<String> items) { }
// items() returns the original List — caller can mutate it
```

Better:

```java
public record Data(List<String> items) {
    public List<String> items() {
        return List.copyOf(items); // defensive copy
    }
}
```

---

# 10) Example: realistic DTO with validation and derived property

```java
import java.util.List;

public record User(String username, String email, List<String> roles) {

    public User {
        if (username == null || username.isBlank()) {
            throw new IllegalArgumentException("username required");
        }
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("invalid email");
        }
        // defensive copy for mutable components
        roles = roles == null ? List.of() : List.copyOf(roles);
    }

    public boolean isAdmin() {
        return roles.contains("ADMIN");
    }

    // convenience factory
    public static User of(String username, String email) {
        return new User(username, email, List.of());
    }
}
```

Usage:

```java
User u = User.of("john", "john@example.com");
System.out.println(u);              // User[username=john, email=john@example.com, roles=[]]
System.out.println(u.isAdmin());    // false
```

---

# When to use records

Use records when:

- You need a simple immutable data carrier (DTO, value object, key).
- You want less boilerplate and safer, clearer code.
- You don’t need to support subclassing or maintain large mutable internal state.

Prefer classes if:

- You need mutable fields,
- You need fine-grained control over encapsulation beyond what records were designed for,
- You need to expose different semantics than a straightforward data carrier.
