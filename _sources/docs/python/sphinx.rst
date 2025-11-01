Sphinx Setup
============

1. Create a virtual environment and activate it.

.. code-block:: bash
    
    python3 -m venv sphinxenv

2. Create HTML documentation.

.. tabs::

    .. tab:: iOS and Unix based Systems

        .. code-block::

            source sphinxenv/bin/activate

    .. tab:: Windows

        .. code-block::

            .\sphinxenv\Scripts\activate

3. Create Sphinx Site 

.. code-block:: sh

    sphinx-quickstart


4. Install Sphinx, Sphinx Read the Docs extension and other extensions.

.. code-block:: sh
  :caption: requirements.txt

  sphinx
  sphinx_rtd_theme
  sphinx_code_tabs
  sphinx_copybutton
  sphinx-tabs
  recommonmark
  nbsphinx
  sphinx-markdown-tables
  pandoc
  sphinx-reredirects
  sphinxcontrib.asciinema
  sphinxemoji
  sphinx-autobuild
  sphinx-multiversion

5. Install Libs 

.. code-block:: sh

  pip install -r requirements.txt

6. Create HTML documentation.

.. tabs::

  .. tab:: iOS and Unix based Systems

      .. code-block::

        make html

  .. tab:: Windows

      .. code-block::

        .\make.bat html
    
7. Choose the theme for your convinience from below website.

.. seealso::

  `Sphinx Themes <https://sphinx-themes.org/>`_


