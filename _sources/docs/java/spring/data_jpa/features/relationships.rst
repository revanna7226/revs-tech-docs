Spring Data JPA Relationships
====================================

Introduction:
    In real-world applications, entities are often related to one another.
    **Spring Data JPA** (via JPA/Hibernate) supports different types of relationships between entities, 
    allowing you to model and manage complex data structures effectively.

    The three main types of relationships are:

    1. **One-to-One**
    2. **One-to-Many / Many-to-One**
    3. **Many-to-Many**

    Each relationship type is defined using specific annotations such as:
    ``@OneToOne``, ``@OneToMany``, ``@ManyToOne``, and ``@ManyToMany``.

1. One-to-One Relationship:
    **Definition:**
    A **One-to-One** relationship means that one entity is associated with exactly one other entity.

    **Example Scenario:**  
    Each *Employee* has one *Address*.

    **Entity Classes:**

    .. code-block:: java

        import jakarta.persistence.*;

        @Entity
        public class Employee {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;

            @OneToOne(cascade = CascadeType.ALL)
            @JoinColumn(name = "address_id", referencedColumnName = "id")
            private Address address;

            // Getters and Setters
        }

        @Entity
        public class Address {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String city;
            private String state;

            // Getters and Setters
        }

    **Explanation:**

    - ``@OneToOne`` defines the relationship.
    - ``@JoinColumn`` specifies the foreign key column (``address_id``).
    - ``cascade = CascadeType.ALL`` ensures both entities persist or delete together.

2. One-to-Many and Many-to-One Relationship:
    **Definition:**

    - A **One-to-Many** relationship occurs when one entity is related to multiple instances of another entity.
    - The reverse side (**Many-to-One**) indicates that multiple entities share one reference to another entity.

    **Example Scenario:**  
    
    One *Department* can have multiple *Employees*,  
    but each *Employee* belongs to only one *Department*.

    **Entity Classes:**

    .. code-block:: java

        // Inverse side (non-owning): Refers to the owning side, does not manage the foreign key.
        @Entity
        public class Department {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;

            @OneToMany(mappedBy = "department", cascade = CascadeType.ALL)
            // “This list of employees is mapped by the department field in the Employee entity.”
            private List<Employee> employees;

            // Getters and Setters
        }

        // Owning side of the relationship:
        // Responsible for managing the foreign key column in the database.
        @Entity
        public class Employee {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;

            @ManyToOne
            @JoinColumn(name = "department_id")
            private Department department;

            // Getters and Setters
        }

    Generated Tables in the database is as follows,        

    .. code-block:: sql

        CREATE TABLE `department` (
            `id` bigint NOT NULL AUTO_INCREMENT,
            `name` varchar(255) DEFAULT NULL,
            PRIMARY KEY (`id`)
        );

        CREATE TABLE `employee` (
            `id` bigint NOT NULL AUTO_INCREMENT,
            `name` varchar(255) DEFAULT NULL,
            `department_id` bigint DEFAULT NULL,
            PRIMARY KEY (`id`),
            FOREIGN KEY (`department_id`) REFERENCES `department` (`id`)
        )

    **Explanation:**

    - ``@OneToMany`` on the Department side indicates multiple employees.
    - ``mappedBy = "department"`` tells JPA that the relationship is owned by the ``Employee`` entity.
    - ``@ManyToOne`` on the Employee side defines the owner of the relationship.
    - The join column ``department_id`` will be created in the ``employee`` table.

    **Example Usage:**

    .. code-block:: java

        Department dept = new Department();
        dept.setName("IT");

        Employee emp1 = new Employee();
        emp1.setName("Alice");
        emp1.setDepartment(dept);

        Employee emp2 = new Employee();
        emp2.setName("Bob");
        emp2.setDepartment(dept);

        dept.setEmployees(List.of(emp1, emp2));

        departmentRepository.save(dept);

3. Many-to-Many Relationship:
    **Definition:**

    A **Many-to-Many** relationship occurs when multiple records of one entity are related to multiple records of another entity.

    **Example Scenario:** 

    A *Student* can enroll in multiple *Courses*, and a *Course* can have multiple *Students*.

    **Entity Classes:**

    .. code-block:: java

        import jakarta.persistence.*;
        import java.util.Set;

        @Entity
        public class Student {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String name;

            @ManyToMany
            @JoinTable(
                name = "student_course",
                joinColumns = @JoinColumn(name = "student_id"),
                inverseJoinColumns = @JoinColumn(name = "course_id")
            )
            private Set<Course> courses;

            // Getters and Setters
        }

        @Entity
        public class Course {

            @Id
            @GeneratedValue(strategy = GenerationType.IDENTITY)
            private Long id;

            private String title;

            @ManyToMany(mappedBy = "courses")
            private Set<Student> students;

            // Getters and Setters
        }

    **Explanation:**

    - ``@ManyToMany`` defines a bidirectional many-to-many relationship.
    - ``@JoinTable`` defines a join table (``student_course``) with foreign keys ``student_id`` and ``course_id``.
    - ``mappedBy = "courses"`` makes the ``Course`` entity the inverse side of the relationship.

    **Example Usage:**

    .. code-block:: java

        Student s1 = new Student();
        s1.setName("John");

        Student s2 = new Student();
        s2.setName("Mary");

        Course c1 = new Course();
        c1.setTitle("Spring Boot");

        Course c2 = new Course();
        c2.setTitle("Microservices");

        s1.setCourses(Set.of(c1, c2));
        s2.setCourses(Set.of(c1));

        studentRepository.save(s1);
        studentRepository.save(s2);

Cascade Types in Relationships:
    Cascade Types control how operations propagate from parent to child entities.

    +-----------------------+--------------------------------------------------------+
    | Cascade Type          | Description                                            |
    +=======================+========================================================+
    | ALL                   | Applies all cascade operations                         |
    +-----------------------+--------------------------------------------------------+
    | PERSIST               | Propagates persist operation                           |
    +-----------------------+--------------------------------------------------------+
    | MERGE                 | Propagates merge operation                             |
    +-----------------------+--------------------------------------------------------+
    | REMOVE                | Propagates delete operation                            |
    +-----------------------+--------------------------------------------------------+
    | REFRESH               | Propagates refresh operation                           |
    +-----------------------+--------------------------------------------------------+
    | DETACH                | Propagates detach operation                            |
    +-----------------------+--------------------------------------------------------+

Example:
``@OneToMany(cascade = CascadeType.ALL)`` ensures saving the parent also saves the child entities.

Fetch Types:
    Fetch type determines how related entities are loaded.

    +------------------+---------------------------------------------+
    | Fetch Type       | Description                                 |
    +==================+=============================================+
    | LAZY (default)   | Loads the relationship only when accessed   |
    +------------------+---------------------------------------------+
    | EAGER            | Loads the relationship immediately          |
    +------------------+---------------------------------------------+

    Example:
        ``@OneToMany(fetch = FetchType.LAZY)`` — Child entities are loaded on-demand.

Summary:
    **Spring Data JPA Relationships** allow mapping of object associations to relational database tables.
    **Key Takeaways:**

    - ``@OneToOne`` → One entity associated with one other.
    - ``@OneToMany`` / ``@ManyToOne`` → One entity related to multiple others.
    - ``@ManyToMany`` → Many entities related to many others.
    - Relationships can be **bidirectional** or **unidirectional**.
    - Use **cascade** and **fetch types** carefully to control persistence behavior and performance.

Conclusion:
    Understanding and correctly implementing entity relationships in Spring Data JPA 
    is essential for designing robust and maintainable data models.
