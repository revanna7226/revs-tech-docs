Optional in Java
================

What is ``Optional``?
---------------------

``Optional<T>`` is a **container object** introduced in **Java 8** that may or may not 
contain a non-null value.  
It helps avoid ``NullPointerException`` and makes code more readable when dealing 
with potentially missing values.

Think of it as a wrapper around a value that says:  
➡️ "This value might be there, or it might not."

Creating an Optional
--------------------

.. code-block:: java

   // Empty Optional
   Optional<String> empty = Optional.empty();

   // Non-null value
   Optional<String> name = Optional.of("Revannaswamy");

   // Nullable value (safe wrapper)
   Optional<String> nullable = Optional.ofNullable(null); // won't throw NPE

Common Methods in ``Optional``
------------------------------

#. **isPresent() / isEmpty()**

   Check if a value exists.

   .. code-block:: java

      Optional<String> opt = Optional.of("Java");
      System.out.println(opt.isPresent()); // true
      System.out.println(opt.isEmpty());   // false
    
#. **get()** ❌ (Not recommended)

   Directly fetch the value — throws ``NoSuchElementException`` if empty.

   .. code-block:: java

      Optional<String> opt = Optional.of("Java");
      System.out.println(opt.get()); // Java

   ⚠️ Avoid ``get()``, prefer safer alternatives.

#. **orElse()**

   Provide a default value if empty.

   .. code-block:: java

      Optional<String> opt = Optional.ofNullable(null);
      System.out.println(opt.orElse("Default")); // Default

#. **orElseGet()**

   Like ``orElse()``, but uses a **Supplier** (lazy evaluation).

   .. code-block:: java

      Optional<String> opt = Optional.ofNullable(null);
      System.out.println(opt.orElseGet(() -> "Generated")); // Generated

#. **orElseThrow()**

   Throw exception if empty.

   .. code-block:: java

      Optional<String> opt = Optional.ofNullable(null);
      String value = opt.orElseThrow(() -> new IllegalArgumentException("Value missing"));

#. **ifPresent()**

   Run code if value exists.

   .. code-block:: java

      Optional<String> opt = Optional.of("Java");
      opt.ifPresent(val -> System.out.println("Value: " + val));

#. **ifPresentOrElse()** (Java 9+)

   Run one action if present, another if empty.

   .. code-block:: java

      Optional<String> opt = Optional.empty();
      opt.ifPresentOrElse(
         val -> System.out.println("Value: " + val),
         () -> System.out.println("No value found")
      );

#. **map()**

   Transform the value if present.

   .. code-block:: java

      Optional<String> opt = Optional.of("java");
      Optional<String> upper = opt.map(String::toUpperCase);
      System.out.println(upper.get()); // JAVA

#. **flatMap()**

   Like ``map()``, but avoids nested ``Optional``.

   .. code-block:: java

      Optional<String> opt = Optional.of("Java");
      Optional<Integer> length = opt.flatMap(val -> Optional.of(val.length()));
      System.out.println(length.get()); // 4

#. **filter()**

   Keep value only if condition matches.

   .. code-block:: java

      Optional<String> opt = Optional.of("Java");
      opt.filter(val -> val.startsWith("J"))
      .ifPresent(System.out::println); // Java

Practical Example
-----------------

Suppose we have a ``User`` with an optional email.

.. code-block:: java

   class User {
      private String name;
      private String email;

      public User(String name, String email) {
         this.name = name;
         this.email = email;
      }

      public Optional<String> getEmail() {
         return Optional.ofNullable(email);
      }
   }

   public class OptionalDemo {
      public static void main(String[] args) {
         User user = new User("Revs", null);

         // Get email safely
         String email = user.getEmail()
                              .orElse("no-email@default.com");

         System.out.println(email); // no-email@default.com
      }
   }

Summary
-------

* ``Optional`` is a **container** to avoid null checks.
* Provides safe methods (``orElse``, ``map``, ``filter``, etc.).
* Should be used **for return types**, not for fields/parameters.
* Helps make APIs **explicitly state** "value may be absent."
