Introduction
=============

- Go is statically/strictly typed object.
- Go is not Object oriented Programming Language.


Environment Setup
-----------------
1. Golang
   
   Download and install an appropriate variant of Go from `Go.org <https://go.dev/dl/>`_.
   
2. VS Code Editor
3. VS Code Extensions
   
   Search and install *Go* which has millions of download.

4. Open VSCode and start a project for Go. Create a file with an extention .go. Check the language mode by checking at the bottom of the VSCode Editor window. If you are seeing some other language then click on it and select Go.

First Code in Go
----------------

.. code-block:: go
  
  // main.go

  package main

  import "fmt"

  func main() {
    fmt.Println("Hello world!")
  }


How do we run the code in the Project?
--------------------------------------

1. Navigate to project directory.
2. Run command to run go code

.. code-block:: bash

  go run main.go

3. Some of the Go CLI commands are listed in the below table.
   
.. list-table:: Go CLI
   :widths: 50 50
   :header-rows: 1

   * - Go Command
     - Comments
   * - go run
     - Compiles and executes one or two files
   * - go build
     - Compiles a bunch of go source code files and creates .exe file.
   * - go fmt
     - Formats all the codes in each file in the current directory.
   * - go install
     - Compiles and installs a package
   * - go get
     - Downloads the raw source code of someone else's package
   * - go test
     - Runs any tests associated with the current project 

What does `package main` mean?
------------------------------

1. Package == Project == Workspace
2. Each Go files should declares `package` at the beginning.
3. There are two types of `packages` in Go.
   - Executable/main: Generates a file that we can double click and run.
   - Reusable/non-main: codes used as `helpers`. It's a good place to put reusable logic.

1. Executable
+++++++++++++

package main

"main" is special

Defines a package that can be compiled and then *executed*. Must have a func called 'main'.

2. Reusable
+++++++++++

package calculator
package uploader

Defines a package that can be used as a dependency (helper code)

What does `import "fmt"` mean?
------------------------------
`Go standard libraries <https://pkg.go.dev/std>`_

`fmt` is the default/standard library package which is writen in Go. We can use all the functionality in our packages by importing it in our package.

`fmt` stands for format used to print out different data in Go.
