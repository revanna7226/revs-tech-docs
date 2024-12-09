Testing in go
--------------

Creating a go.mod File
======================

In order to run the test we must create a go.mod file. Otherwise, you will see an error thrown:

.. error:: go: go.mod file not found in current directory or any parent directory; see 'go help modules'


Inside the cards project directory run the following:

.. code-block:: bash

    go mod init cards

Then, you will be able to use the run test function from within VSCode, and/or run go test from the terminal.

