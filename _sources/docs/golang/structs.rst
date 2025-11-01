Struct in Go
-------------

Struct is a datastructure in Go. Collection of properties that are related togethet.

.. code-block:: go

    package main

    // defining a struct in go
    type person struct {
        firstName string
        lastname string
    }

    func main() {
        // creating struct var
        var alex person
        fmt.Println(alex) // {}
        fmt.Println("%+v", alex) // {firstName, lastname}

        // creating struct values
        // Option #1
        alex := person{"Alex", "Anderson"}

        // Option #2
        alex := person{firstName:"Alex", lastname:"Anderson"}

        fmt.Println(alex) // {Alex Anderson}

        // reassignment
        alex.firstName = "Alen"
    }

Default values
==============
.. list-table:: Default values of struct property
   :widths: 50 50
   :header-rows: 1

   * - Type
     - Zero Value
   * - string
     - ""
   * - int
     - 0
   * - float
     - 0
   * - bool
     - false

Embedding struct
================

.. code-block:: Go

    type contactInfo struct {
        email   string
        zipCode int
    }

    type person struct {
        firstName string
        lastName  string
        contactInfo
    }

    func main() {
        jim := person{
            firstName: "Jim",
            lastName:  "Party",
            contactInfo: contactInfo{
                email:   "jim@gmail.com",
                zipCode: 94000,
            },
        }

        fmt.Printf("%+v", jim)
    }

Structs with Receiver Function
==============================

.. code-block:: go

    func main() {
        alex := { ... }

        alex.updateName("Alexander")
        alex.print()
    }

    // does not update the object
    func (p person) updateName(newFirstName string) {
	    p.firstName = newFirstName
    }

    func (p person) print() {
	    fmt.Printf("%+v", p)
    }

.. warning:: Pass by Value will copy the object and change it's value rather than updating actual object.

Pointers in Struct
==================

.. code-block:: go

    func main() {
        alexPointer := &alex
        alexPointer.updateName("Alexander")
        alex.print()
    }

    // *person -> This is a type description - it means we're working with a pointer to a person
    func (pointerToPerson *person) updateName(newFirstName string) {
        // *pointerToPerson -> This is an operator - it means we want to manipulate the value the pointer is referencing
        (*pointerToPerson).firstName = newFirstName
    }

.. note:: 

    - &variable -> Give me the memory address of the value this variable is pointing at
    - *pointer -> Give me the value this memory address is pointing at

Pointers shortcut
=================

.. code-block:: go

    func main() {
        // ~alexPointer := &alex~
        alex.updateName("Alexander")
        alex.print()
    }

    // *person -> This is a type description - it means we're working with a pointer to a person
    func (pointerToPerson *person) updateName(newFirstName string) {
        // *pointerToPerson -> This is an operator - it means we want to manipulate the value the pointer is referencing
        (*pointerToPerson).firstName = newFirstName
    }
    


Gotchas of pointer
==================

- With 'value types' in Go, we have to worry about pointers if we want to pass a value to a function and modify the original value inside the function
- When we create a slice, Go will automatically create which two data structures. An Array and a structure that records the length of the slice, the capacity of the slice and a reference to the underlying array.



.. list-table:: .
   :widths: 30 30 40
   :header-rows: 1

   * - Data Types
     - Types
     - Comments
   * - Value Types
     - int, float, string, bool and structs
     - Use pointers to change these things in a function. With 'value types' in Go, we have to worry about pointers if we want to pass a value to a function and modify the original value inside the function
   * - Reference Types
     - slices, maps, channels, pointers and functions
     - With 'reference types' in Go, do not we have to worry about pointers if we want to pass a value to a function and modify the original value inside the function

.. image:: /_static/go/images/sliceinfunction.png
