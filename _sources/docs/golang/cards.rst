Cards Project
-------------

Variable Declaration
++++++++++++++++++++

.. code-block:: go

    var card string = "Ace of Spades"
    // or
    card := "Ace of Spades"

Basic Go types
~~~~~~~~~~~~~~

.. list-table:: Basic Go Types
   :widths: 50 50
   :header-rows: 1

   * - Type
     - Example
   * - bool
     - true, false
   * - string
     - "Hi!", "Good morning!"
   * - int
     - 0, -10000, 99999
   * - float64
     - 10.000001, 0.00009, -100.003

Arrays and Slice
~~~~~~~~~~~~~~~~

Arrays
******

- Fixed length list of things
- Every element in an array must of same


Slice
*****
- An array that can grow or shink
- Slices are zero indexed.
- Examples

  .. code-block:: go

    // declaring new slice of strings
    cards := []string{"Ace of spades", newCard()}

    // adding element to the end of slice
    cards = append(cards, "Six of Spades")

    // function which returns newCard of string value
    func newCard() string {
      return "Five of Diamonds"
    }

  .. code-block:: go

    fruits := []string{"apple", "banana", "grape", "orange"}

    // slicing of any array/slice
    // fruits[start Index Including : end Index Not inlcuding]
    firstTwoFruits = fruits[0:2] // apple, banana

    fruits[:2] // beginning to 2nd index but 2nd index not included
    friuts[2:] // from second index to last element
  

Iterating Arrays or Slice
~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: go

  for index, card := range cards {
    fmt.Println(card)
  }

OO Approach and Go Approach
~~~~~~~~~~~~~~~~~~~~~~~~~~~

- We know string, int, bool, array and maps are Basic Go Types.
- We want to `extend` a base type and add some extra functionality to it.
- Tell Go we want to create an array of strings and adda bunch of functions specifically made to work with it.
  
  .. code-block:: go

    // craete a new custom type deck 
    // which is of type slice of strings
    // means a kind of deck extends []string
    type deck []string
  
- A function with a receiver is like a method or a function that belongs to an instance. A function which only works for type of deck.

Cards Project Structure
~~~~~~~~~~~~~~~~~~~~~~~

- main.go -> Code to create and manipulate a deck
- deck.go -> Code that describes what a deck is and how it Workspace
- deck_test.go -> Code to automatically test the deck

.. code-block:: bash

  // how to run
  go run main.go deck.go

Type Conversion
~~~~~~~~~~~~~~~

