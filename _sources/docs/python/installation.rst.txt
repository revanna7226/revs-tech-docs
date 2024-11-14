Python Installation
===================

1. Install *Anaconda3* which installs **Python** along with it.
2. Pip also installed along with Anaconda3.
3. Create a Project directory.
4. Go to project directory and create Virtual Environment inside the Project directory. Here myenv is the name of the vitual Environment.

.. code-block:: bash
    
    python3 -m venv myenv

5. Source activate file and activate venv.

.. code-block:: bash

    # Windows ds
    # activate virtual environment
    .\sphinx-env\Scripts\activate

    # iOs
    # activate environment
    source myenv/bin/activate

1. Install Packages: Once activated, use pip to install any packages you need:

.. code-block:: sh

    pip3 install package_name

7. To deactivate your environment, simply type:

.. code-block:: sh

    deactivate