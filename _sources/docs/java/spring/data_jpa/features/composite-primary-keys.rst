Composite Primary Keys
========================================

In Spring Data JPA, a *composite primary key* is a primary key that consists of multiple columns.
It uniquely identifies each record in a table using the combination of these columns rather than a single column.

Composite keys can be defined in two main ways:

#. Using `@Embeddable` and `@EmbeddedId`:
    In this approach, you define a separate class annotated with ``@Embeddable`` that contains the key fields.
    This class is then embedded into the entity using ``@EmbeddedId``.

    Example:

    .. code-block:: java

        @Embeddable
        public class OrderId implements Serializable {
            private Long orderId;
            private Long productId;

            // Getters, Setters, hashCode(), equals()
        }

        @Entity
        public class Order {
            @EmbeddedId
            private OrderId id;

            private int quantity;

            // Getters and Setters
        }

    Key Points:
       - The ``@Embeddable`` class must implement ``Serializable``.
       - Must override ``equals()`` and ``hashCode()`` methods.
       - The entity uses ``@EmbeddedId`` to reference the composite key.

#. Using `@IdClass`:
    In this approach, you define a separate class annotated with ``@IdClass``.
    Each field in the ``@IdClass`` corresponds to a field in the entity that is annotated with ``@Id``.

    Example:

    .. code-block:: java

        import java.io.Serializable;

        public class OrderId implements Serializable {
            private Long orderId;
            private Long productId;

            // Getters, Setters, hashCode(), equals()
        }

        @Entity
        @IdClass(OrderId.class)
        public class Order {
            @Id
            private Long orderId;

            @Id
            private Long productId;

            private int quantity;

            // Getters and Setters
        }

Key Points:
   - ``@IdClass`` must map fields with matching names and types to those in the entity.
   - The ID class must be ``Serializable`` and override ``equals()`` and ``hashCode()``.


Best Practices:
  - Prefer ``@Embeddable`` and ``@EmbeddedId`` for cleaner encapsulation.
  - Always implement ``equals()`` and ``hashCode()`` to ensure entity identity works correctly.
  - Avoid using too many columns in a composite key; use surrogate keys if the composite key becomes complex.

References:
   - `Spring Data JPA Reference Documentation <https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.composite-primary-keys>`_
   - `Jakarta Persistence API (JPA) Specification <https://jakarta.ee/specifications/persistence/>`_
