Setting up Angular on Local Machine
===================================

Use npx to Run a Specific Angular CLI Version Without Installing Globally

.. code-block:: bash

    # check the version of Angular CLI
    npx @angular/cli --version
    npm show @angular/cli versions --json

    npm install @angular/cli@15 --save-dev
    npx ng version

    npx @angular/cli@15 new my-angular15-app
    npx @angular/cli@16 new my-angular16-app

    npx ng serve --port=4500
