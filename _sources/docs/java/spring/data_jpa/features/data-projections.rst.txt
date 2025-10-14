Projections
=============================

**Projections** in Spring Data JPA are used to retrieve a subset of entity attributes
instead of fetching the entire entity.  
They are particularly useful for performance optimization when you only need certain fields
from an entity, avoiding unnecessary data fetching and improving query efficiency.

Spring Data JPA supports three main types of projections:
   - **Interface-based Projections**
   - **Class-based (DTO) Projections**
   - **Dynamic Projections**


#. Interface-based Projections:

   In this approach, you define an interface that exposes getters corresponding
   to the entity fields you want to fetch.  
   Spring Data JPA automatically maps query results to this interface.

   Example:

   .. code-block:: java

      // Entity
      @Entity
      public class Person {
         @Id
         @GeneratedValue(strategy = GenerationType.IDENTITY)
         private Long id;

         private String name;
         private String email;
         private int age;

         // Getters and Setters
      }

      // Projection Interface
      public interface PersonView {
         String getName();
         String getEmail();
      }

      // Repository
      public interface PersonRepository extends JpaRepository<Person, Long> {
         List<PersonView> findByAgeGreaterThan(int age);
      }

   Explanation:
      - The query result will only contain ``name`` and ``email``.
      - Spring Data automatically maps the query result to the ``PersonView`` projection.
      - Only the selected columns are retrieved from the database.


#. Class-based (DTO) Projections:

   You can use a **DTO (Data Transfer Object)** class to represent specific fields.
   Spring Data JPA maps query results to the constructor of the DTO.

   Example:

   .. code-block:: java

      // DTO Class
      public class PersonDTO {
         private String name;
         private String email;

         public PersonDTO(String name, String email) {
               this.name = name;
               this.email = email;
         }

         // Getters
      }

      // Repository with JPQL query
      public interface PersonRepository extends JpaRepository<Person, Long> {

         @Query("SELECT new com.example.demo.PersonDTO(p.name, p.email) FROM Person p WHERE p.age > :age")
         List<PersonDTO> findPersonDetailsByAge(@Param("age") int age);
      }

   Explanation:
      - The JPQL query uses the ``new`` keyword to instantiate the DTO.
      - The DTO constructor parameters must match the selected fields in order and type.
      - This is more flexible and allows complex mappings or transformations.


#. Dynamic Projections:

   Dynamic projections allow you to choose the projection type dynamically at runtime
   using generics in the repository method.

   Example:

   .. code-block:: java

      // Repository
      public interface PersonRepository extends JpaRepository<Person, Long> {
         <T> List<T> findByAgeGreaterThan(int age, Class<T> type);
      }

      // Usage in Service Layer
      @Service
      public class PersonService {

         @Autowired
         private PersonRepository repository;

         public void example() {
               List<PersonView> viewProjection = repository.findByAgeGreaterThan(25, PersonView.class);
               List<PersonDTO> dtoProjection = repository.findByAgeGreaterThan(25, PersonDTO.class);
         }
      }

   Explanation:
      - The same repository method can return either an interface-based or DTO projection.
      - Provides flexibility for different use cases (e.g., API responses, admin views).

Key Advantages:
   - **Performance Optimization:** Reduces unnecessary data fetching.
   - **Improved Query Efficiency:** Fetch only the required fields.
   - **Flexible Design:** Supports both static and dynamic projections.
   - **Encapsulation:** Prevents exposing the full entity in API responses.

Best Practices:
   - Use interface-based projections for simple field selection.
   - Use DTO projections for complex transformations or computed values.
   - Always ensure projection fields match entity property names (for automatic mapping).
   - Use dynamic projections when multiple view types are required for the same query.


References:
  - `Spring Data JPA Reference Documentation <https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#projections>`_
  - `Jakarta Persistence Specification <https://jakarta.ee/specifications/persistence/>`_