Maps in Go
----------

- Map in Go is collection of key values pairs.
- It is very similer to Hash in Ruby, Objects in Javascript and Dict in Python.
- Keys and Values must be of same types.

.. code-block:: go

    // declaring map var
    var colors map[string]string

    // or
    colors := make(map[string]string)

    // declaring and creating map
    colors := map[string]string {
        "red": "#ff0000",
        "green": "#4bf763"
    }

    // updating map
    colors["white"] = "#ffffff"

    // delete key value pair    
    delete(colors, "white")

    for key, value := range colors {
        fmt.Println(key, value)
    }

Maps vs Struct
==============

Map
++++
  - All keys must be the same type
  - All Values must be the same type
  - Keys are indexed, we can iterate over them
  - Use to represent a collection of related properties
  - Don't need to know all the keys at compile time
  - Map is a reference type

Struct
++++++
  - Values can be of different type
  - Keys does not support indexing
  - You need to know all the different fields at compile time
  - Use to represent a thing with a lot of different properties
  - Struct is a value type.
