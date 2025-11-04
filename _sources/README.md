# Sphinx-Revs-Tech-Docs

This is Sphinx Doc Project which generates Web App for Revs Tech Doc.

Revs Tech Doc is live [here](https://revs-tech-docs.netlify.app).

## How to set up this project on your local machine?

1. Install Python3.
2. Pip also installed along with Python3.
3. Create a Project directory.
4. Setup VSCode IDE and Open the Project.
5. Install VSCode Plug-ins for Sphinx(Esbonio, reStructuredText and reStructuredText Syntax highlighting)
6. Go to project directory and create Virtual Environment inside the Project directory. Here sphinx-env is the name of the vitual Environment.

```bash
    python3 -m venv sphinx-env
```

7. Activate venv.

```bash
    # Windows ds
    # activate virtual environment
    .\sphinx-env\Scripts\activate

    # iOs
    # activate environment
    source myenv/bin/activate
```

8. Install Sphinx Packages: Once activated, use pip to install any packages you need:

```bash
    pip3 install sphinx
    pip3 install sphinx_rtd_theme
    pip3 install sphinx_code_tabs
    pip3 install sphinx_copybutton
    pip3 install sphinx-tabs
    pip3 install recommonmark
    pip3 install nbsphinx
    pip3 install sphinx-markdown-tables
    pip3 install pandoc
    pip3 install sphinx-reredirects
    pip3 install sphinxcontrib.asciinema
    pip3 install sphinxemoji
    pip3 install sphinx-autobuild
    pip3 install sphinx-multiversion
```

9. Build HTML Project by running.

```bash
    make html
```

10. To deactivate your environment, simply type:

```bash
    deactivate
```
