Github
======

`Official Website <https://github.com>`_

.. list-table:: About Git and GitHub
   :widths: 50 50
   :header-rows: 1

   * - Git
     - GitHub
   * - Version Control System
     - Largest Development Platform
   * - Manages code history
     - Cloud Hosting and Collaboration Provider
   * - Tracks changes
     - Git Repository Hosting
   

.. image:: /_static/images/git-github.png

.. code-block:: bash

    # pushes the commits to remote repo
    git push origin <branch>

    # builds the connection between local git and remote repo
    git remote add origin <remote-repo-url>

    # pulls the latest changes from remote repo
    git pull

.. note:: 

    origin -> is the name of the remote machine or alias/shorthand of the URL of the remote repository.

Personal Access Token
---------------------

The Personal Access Token can be generated from the settings and this can used to authorize the commits to remote repo from local and for other actions.

The Personal Access Token will be asked one time only when we push our first change to remote.

On Windows we need to input PAT for Pop and In case of Mac OS we have to input PAT via Terminal.

PAT input to Windows will be stored in Windows Credential Manager and it can be removed to load new token.

.. note:: 

    For MacOS users, the credentials can be updated via the terminal as follows:

    git credential-osxkeychain erase PRESS RETURN KEY

    host=github.com PRESS RETURN KEY

    protocol=https PRESS RETURN KEY

    PRESS RETURN KEY

    PRESS RETURN KEY

    ---

    If you try to push to the remote repository afterwards, you will be prompted to enter your credentials.


Local - Remote Workflows
------------------------

Remote Tracking branch is local copy of remote branch. which sits in between local and remote branches.

.. code-block:: bash

    # list all branches in local
    git branch

    # list all local branches and remote tracking branches
    git branch -a

    # to list all remote branches of the repo
    git ls-remote


.. image:: /_static/images/local-remote-workflow.png


.. note:: 
    
    git pull -> git fetch + git merge
    
    git fetch -> fetches the changes from remote branches to local remote tracking branches

    git merge -> merges the remote tracking branches to local branches


Branch Types
------------

.. image:: /_static/images/branch-types.png

.. image:: /_static/images/local-remote-tracking-branches.png
    



    


