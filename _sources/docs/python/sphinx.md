# Sphinx Setup

1. Create a virtual environment and activate it.

   ```bash
   python3 -m venv sphinxenv
   ```

1. Activate Enviroment

   ::::{tab-set}

   :::{tab-item} iOS/Unix based Systems

   ```bash
   source sphinxenv/bin/activate
   ```

   :::

   :::{tab-item} Windows

   ```bash
   .\sphinxenv\Scripts\activate
   ```

   :::

   ::::

1. Install Sphinx, Sphinx Read the Docs extension and other extensions.

   ```{code-block} text
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
   ```

1. Install the libraries

   ```bash
   pip install -r requirements.txt
   ```

1. Create Sphinx Site

   ```bash
   sphinx-quickstart
   ```

1. Create HTML documentation.

   ::::{tab-set}

   :::{tab-item} iOS/Unix based Systems

   ```bash
   make html
   ```

   :::

   :::{tab-item} Windows

   ```bash
   .\make.bat html
   ```

   :::

   ::::

1. Open _build/html/index.html_ in your browser.

:::{seealso}

- [{book}theme](https://sphinx-book-theme.readthedocs.io/en/stable/tutorials/get-started.html)
- [MyST - Markedly Structured Text - Parser](https://myst-parser.readthedocs.io/en/latest/index.html)
- [Sphinx Themes Gallery](https://sphinx-themes.org/)
- [Format Markdown Table](https://tabletomarkdown.com/format-markdown-table/)
  :::

Here’s your content correctly formatted as a **Markdown table** 👇

---

## ✅ Difference Between Checked and Unchecked Exceptions in Java

| **Criteria**              | **Checked Exceptions**                                                                               | **Unchecked Exceptions**                                                                                    |
| ------------------------- | ---------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **Definition**            | Exceptions that are **checked at compile-time** by the Java compiler.                                | Exceptions that are **not checked at compile-time**, they occur at **runtime**.                             |
| **Superclass**            | Subclasses of `Exception` _(excluding `RuntimeException`)_                                           | Subclasses of `RuntimeException`                                                                            |
| **Compile-Time Checking** | Must be either **handled using `try-catch`** or **declared using `throws`** in the method signature. | Compiler does **not force** you to handle or declare them.                                                  |
| **Occurrence**            | Generally caused by **external factors** (e.g., file not found, network errors).                     | Usually caused by **programming mistakes** (e.g., null pointer, division by zero).                          |
| **Examples**              | `IOException`, `SQLException`, `FileNotFoundException`, `ClassNotFoundException`                     | `NullPointerException`, `ArithmeticException`, `ArrayIndexOutOfBoundsException`, `IllegalArgumentException` |
| **Handling Requirement**  | **Mandatory** to handle or declare.                                                                  | **Optional** to handle.                                                                                     |

---
