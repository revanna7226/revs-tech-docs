Spring Data JPA Enum Mapping
============================

Introduction:
    In **Spring Data JPA**, it is common to use **enumerations (enums)** to represent a fixed set of constants — 
    such as **Status**, **Role**, or **Priority**.  

    By default, JPA provides an elegant way to **map enums to database columns**, 
    so you can store their values in a structured and readable format.

Why Use Enums in JPA Entities?:
    Enums are strongly typed constants that improve **code readability**, **type safety**, 
    and help avoid errors due to invalid values.

    For example:

    .. code-block:: java

        public enum Status {
            ACTIVE,
            INACTIVE,
            PENDING
        }

    You can use this enum in your entity instead of plain strings or integers, 
    ensuring that only valid status values are stored in the database.

Mapping Enums in JPA:
    JPA supports two strategies to persist enums:

    1. **EnumType.ORDINAL**
    - Stores the **ordinal value** (integer index starting from 0).
    - Example: ``ACTIVE = 0, INACTIVE = 1, PENDING = 2``

    2. **EnumType.STRING**
    - Stores the **enum name** as a string in the database.
    - Example: ``ACTIVE``, ``INACTIVE``, ``PENDING``

1. EnumType.ORDINAL:
    **Definition:**
    Stores the enum value as an integer representing its position (ordinal) in the enum declaration.

    **Example:**

    .. code-block:: java

        import jakarta.persistence.*;

        public enum Status {
            ACTIVE,
            INACTIVE,
            PENDING
        }

        @Entity
        public class Employee {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;

            @Enumerated(EnumType.ORDINAL)
            private Status status;

            // Getters and Setters
        }

    **Database Representation:**

    +----+----------+--------+
    | ID | Name     | Status |
    +====+==========+========+
    | 1  | John     | 0      |
    +----+----------+--------+
    | 2  | Alice    | 1      |
    +----+----------+--------+

    **Advantages:**

    - Efficient in terms of space (stores integer values).

    **Disadvantages:**

    - **Risky during enum changes.**  
    
    If you reorder or insert new enum constants, the stored numeric values will no longer match correctly.

2. EnumType.STRING:
    **Definition:**
    Stores the enum name as a string in the database. This is the **recommended** approach.

    **Example:**

    .. code-block:: java

        import jakarta.persistence.*;

        public enum Status {
            ACTIVE,
            INACTIVE,
            PENDING
        }

        @Entity
        public class Employee {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;

            @Enumerated(EnumType.STRING)
            private Status status;

            // Getters and Setters
        }

    **Database Representation:**

    +----+----------+----------+
    | ID | Name     | Status   |
    +====+==========+==========+
    | 1  | John     | ACTIVE   |
    +----+----------+----------+
    | 2  | Alice    | INACTIVE |
    +----+----------+----------+

    **Advantages:**
    - Safer and more readable.
    - Enum order changes do not affect stored data.

    **Disadvantages:**
    - Slightly larger storage size compared to integer values.

Default Behavior:
    If you omit the ``@Enumerated`` annotation, JPA **defaults to EnumType.ORDINAL**.  
    Therefore, it is a good practice to **explicitly specify** the enum type to avoid unintended mapping.

    Example:

    .. code-block:: java

        // Defaults to ORDINAL (not recommended)
        private Status status;

    Better:

    .. code-block:: java

        @Enumerated(EnumType.STRING)
        private Status status;

Using Enums in Queries:
    You can use enums in both **JPQL** and **Spring Data derived queries**.

    **Example 1: JPQL Query**

    .. code-block:: java

        @Query("SELECT e FROM Employee e WHERE e.status = :status")
        List<Employee> findByStatus(@Param("status") Status status);

    **Example 2: Derived Query**

    .. code-block:: java

        List<Employee> findByStatus(Status status);

    Usage:

    .. code-block:: java

        List<Employee> activeEmployees = employeeRepository.findByStatus(Status.ACTIVE);

Custom Enum Conversion using AttributeConverter:
    You can use a **custom converter** if you want to store enum values differently (e.g., as a custom code or description).

    **Example:**

    .. code-block:: java

        public enum Priority {
            LOW("L"),
            MEDIUM("M"),
            HIGH("H");

            private final String code;

            Priority(String code) {
                this.code = code;
            }

            public String getCode() {
                return code;
            }

            public static Priority fromCode(String code) {
                for (Priority p : Priority.values()) {
                    if (p.getCode().equals(code)) return p;
                }
                throw new IllegalArgumentException("Invalid code: " + code);
            }
        }

        import jakarta.persistence.AttributeConverter;
        import jakarta.persistence.Converter;

        @Converter(autoApply = true)
        public class PriorityConverter implements AttributeConverter<Priority, String> {

            @Override
            public String convertToDatabaseColumn(Priority priority) {
                return priority != null ? priority.getCode() : null;
            }

            @Override
            public Priority convertToEntityAttribute(String code) {
                return code != null ? Priority.fromCode(code) : null;
            }
        }

    **Entity:**

    .. code-block:: java

        @Entity
        public class Task {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String title;

            private Priority priority;  // Automatically converted using PriorityConverter
        }

    **Database Representation:**

    +----+----------+-----------+
    | ID | Title    | Priority  |
    +====+==========+===========+
    | 1  | Report   | H         |
    +----+----------+-----------+
    | 2  | Meeting  | M         |
    +----+----------+-----------+

    **Explanation:**

    - ``@Converter(autoApply = true)`` tells JPA to apply this converter to all entity fields of type ``Priority``.
    - ``AttributeConverter`` provides custom control over how enums are stored and retrieved.

Summary:
    **Spring Data JPA Enum Mapping** enables seamless persistence of enum types into database columns.

**Mapping Strategies:**
   - ``EnumType.ORDINAL`` → Stores integer index (less safe).
   - ``EnumType.STRING`` → Stores enum name (recommended).
   - ``AttributeConverter`` → Provides custom mapping flexibility.

**Best Practices:**
   - Always specify the ``@Enumerated(EnumType.STRING)`` annotation explicitly.
   - Use custom converters when mapping enums to codes or descriptions.
   - Avoid ``ORDINAL`` unless performance and space optimization are critical.

**Conclusion:**
    By correctly mapping enums in Spring Data JPA, you ensure data consistency, maintainability, 
    and make your domain models more expressive and type-safe.
