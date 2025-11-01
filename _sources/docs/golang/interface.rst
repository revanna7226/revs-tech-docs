Interface
----------
We know that, 

- Every value has a type 
- Every function has to specify the type of its arguments

So does that mean... Every function we ever write has to be rewritten to accommodate different types even if the logic in it is identical?

.. code-block:: go

    package main

    import "fmt"

    type bot interface {
        getGreeting() string
    }

    type englishBot struct{}
    type spanishBot struct{}

    func main() {
        eb := englishBot{}
        sb := spanishBot{}

        printGreeting(eb)
        printGreeting(sb)
    }

    func printGreeting(b bot) {
        fmt.Println(b.getGreeting())
    }

    func (englishBot) getGreeting() string {
        return "Hi there!"
    }

    func (spanishBot) getGreeting() string {
        return "Hola!"
    }

