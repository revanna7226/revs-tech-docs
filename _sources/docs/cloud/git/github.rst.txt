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
1. Local Branch
~~~~~~~~~~~~~~~
A branch in Local repository on our Computer.

2. Remote Tracking Branch
~~~~~~~~~~~~~~~~~~~~~~~~~
These are branches fetched from remote repositories, prefixed with the remote name (e.g., origin/main).
Remote Tracking branch is local read only copy of remote branch(not to be edited). which sits in between local and remote branches. This branch will be created when we push local branch to master for the first time.
There will be no connection between Local and Remote branch and every communication (i.e push & pull, pull = fetch + merge) happens through Remote Tracking Branch.

Example: remotes/origin/master

.. note:: 
  git fetch command updates this branch.

.. code-block:: bash

  # to delete remote tracking branches
  git branch --delete --remote origin/feature

  # to delete remote branch from remote server
  git push origin --delete feature

3. Local Tracking Branch
~~~~~~~~~~~~~~~~~~~~~~~~
This branch is Local reference to remote tracking branch(to be edited).

.. code-block:: bash

  git branch -a
  * feature
    master
    remotes/origin/feature
    remotes/origin/feature-remote
    remotes/origin/master

  # create local tracking branch from remote trackign branch
  # git branch --track <local-tracking-branch> <remote-tracking-branch>
  git branch --track feature-remote origin/feature-remote

  # to list local branch and remote tracking branch in details
  git branch -vv

.. warning:: Local tracking branch name should be same as remote tracking branch.


1. Remote Branch
~~~~~~~~~~~~~~~~
A branch on the Remote server/ GitHub. which is connected to remote tracking branch. Which has the same info of Remote Tracking Branch.

.. warning:: 

  Remote repository's name i.e 'origin' and branch name must always be added.


.. code-block:: bash

    # list all branches in local
    git branch

    # list all local branches and tracking branches
    git branch -a

    # lists only remote branches
    git branch -r

    # lists only local branches
    git branch -l

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
    



    


