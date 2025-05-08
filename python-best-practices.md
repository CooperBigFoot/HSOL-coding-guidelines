# Python Best Practices at hydrosolutions

## 1. Introduction

This document outlines the best practices for writing Python code at hydrosolutions. Our goal is to ensure our codebase is readable, maintainable, consistent, and efficient, facilitating collaboration and reducing technical debt.

**All new projects should use Python 3.10 or newer.** This allows us to leverage modern Python features for better code quality and developer experience.

These guidelines are for everyone who writes Python code at hydrosolutions, regardless of their primary role. While not exhaustive, they cover the most common aspects of Python development. Remember, code is read far more often than it is written.

## 2. Code Style, Formatting & Linting (PEP 8)

We adhere to **PEP 8**, the official style guide for Python code. We use **Ruff** for both code formatting and linting, and **uv** for managing Python environments and packages. This ensures consistency and helps us catch issues early.

**Our Setup:**

1. **Environments & Packages with `uv`:**
    * Create & activate environments: `uv venv .venv && source .venv/bin/activate` (or `.venv\Scripts\Activate.ps1` on Windows).
    * Install Ruff into your project: `uv pip install ruff`

2. **Ruff Configuration (`pyproject.toml`):**
    All projects **must** include a `pyproject.toml` file in the root with the following Ruff configuration:

    ```toml
    [tool.ruff]
    line-length = 88
    target-version = "py310" # Assuming Python 3.10+

    [tool.ruff.lint]
    # E/F (Pyflakes), W (pycodestyle), N (pep8-naming), I (isort),
    # UP (pyupgrade), B (flake8-bugbear), C4 (flake8-comprehensions), SIM (flake8-simplify)
    select = ["E", "F", "W", "N", "I", "UP", "B", "C4", "SIM"]
    ```

**How We Use Ruff:**

* **To Format Your Code:**
    Before committing, run:

    ```bash
    ruff format .
    ```

    This reformats all Python files in the current directory and subdirectories.

* **To Lint Your Code & Autofix Issues:**
    Before committing, run:

    ```bash
    ruff check . --fix
    # Or simply, as 'check' is the default and 'fix' is enabled in pyproject.toml:
    # ruff .
    ```

    This checks for style violations and other issues, automatically fixing what it can.

**Key Style Points Enforced by This Setup:**

* **Naming Conventions:** `lowercase` for functions/variables, `UPPERCASE` for constants, `CapWords` for classes (PEP 8 via Ruff's `N` rules).
* **Line Length:** Max 88 characters (Ruff formatter).
* **Indentation:** 4 spaces (Ruff formatter).
* **Import Order:** Automatically sorted and grouped (Ruff's `I` rules).
* **Other Best Practices:** Many other checks from Pyflakes, pycodestyle, etc., are included.

For detailed guidance on docstrings and comments, please see Section 3. We strongly recommend integrating Ruff into your IDE for real-time feedback and format-on-save functionality.

## 3. Documentation & Docstrings (PEP 257)

Clear documentation is crucial for understanding, using, and maintaining code.

* **Docstrings:**
  * All public modules, functions, classes, and methods **must** have docstrings.
  * **Style Choice & Consistency:** You may use any common Python docstring style (e.g., [Google style](https://google.github.io/styleguide/pyguide.html#38-comments-and-docstrings), [NumPy style](https://numpydoc.readthedocs.io/en/latest/format.html), or [reStructuredText/Sphinx style](https://www.python.org/dev/peps/pep-0287/)).
  * **The most important rule is consistency.** Once a style is chosen for a project, it **must** be applied consistently throughout that codebase.
  * **Essential Content:** Regardless of the chosen style, a docstring should clearly explain:
    * The purpose of the module, class, function, or method.
    * Its arguments/parameters (if any), including their types.
    * What it returns (if anything), including the type.
    * Any exceptions it might explicitly raise.
  * **Example (illustrating content using Google style as a reference):**
    The following example shows the *type of information* a good docstring conveys. The exact formatting keywords (like `Args:`, `Returns:`) will depend on the style you choose.

    ```python
    def calculate_flow_rate(area: float, velocity: float) -> float:
        """Calculates the flow rate given area and velocity.

        This function takes the cross-sectional area and the average velocity
        of a fluid to determine its volumetric flow rate.

        Args:
            area (float): The cross-sectional area in square meters.
            velocity (float): The average velocity of the fluid in meters per second.

        Returns:
            float: The calculated flow rate in cubic meters per second.

        Raises:
            ValueError: If area or velocity are negative.
        """
        if area < 0 or velocity < 0:
            raise ValueError("Area and velocity must be non-negative.")
        return area * velocity
    ```

* **Inline Comments:**
  * Use inline comments to explain non-obvious parts of the code – the *why*, not necessarily the *what* if the code is already clear.
  * Write complete sentences for clarity.
  * Keep comments up-to-date with any code changes.
  * Use standard markers like `# TODO:` for planned work and `# FIXME:` for known issues that require attention.

## 4. Type Hinting (PEP 484+)

Type hints significantly improve code readability, help catch potential errors early directly within your editor, and enhance IDE support (like auto-completion and refactoring). We **strongly encourage** their consistent use.

* Add type hints to all new function signatures (arguments and return types).
* Use type hints for variables where the type is not immediately obvious, especially for complex data structures.
* **Use Built-in Generic Types:** For standard collections, use the built-in generic types (e.g., `list[int]`, `dict[str, float]`, `tuple[int, str]`). These are available from Python 3.9+.
* The `typing` module is still necessary for more specialized types not available as built-in generics, such as `Callable`, `Any`, `TypeVar`, `NewType`, `Protocol`, and for `Optional` if you prefer it over `X | None`.
* Since we use Python 3.10+, you can use the `|` operator for union types (e.g., `int | float` instead of `typing.Union[int, float]`, and `str | None` instead of `Optional[str]`).
* **Example:**

    ```python
    def silly_greeting(name: str, enthusiasm_level: int) -> str:
        """
        Generates a silly greeting with a specified level of enthusiasm.

        Args:
            name (str): The name of the person to greet.
            enthusiasm_level (int): An integer representing the level of enthusiasm
                                    (e.g., number of exclamation marks).

        Returns:
            str: A silly greeting string.

        Raises:
            ValueError: If enthusiasm_level is not positive.
        """
        if enthusiasm_level <= 0:
            raise ValueError("Enthusiasm level must be a positive number!")
        
        greeting_phrase= "Woohoo"
        exclamations = "!" * enthusiasm_level
        
        return f"{greeting_phrase}, {name}{exclamations} Welcome to the fun zone!"
    ```

* **IDE Type Checking:** We primarily leverage IDE-based type checking. For VS Code users (with the Python extension and Pylance), ensure your settings enable basic type checking. You can set this in your VS Code `settings.json` (workspace or user):

    ```json
    {
        "python.analysis.typeCheckingMode": "basic"
    }
    ```

    This will provide valuable feedback on type mismatches directly in your editor.

Certainly! Here's a revised version of Section 5, tailored to your company's use of `uv` for virtual environment and dependency management:

---

## 5. Virtual Environments & Dependency Management

We utilize **`uv`** as our primary tool for managing Python virtual environments and dependencies. It serves as a high-performance, all-in-one replacement for tools like `pip`, `pip-tools`, `virtualenv`, and `pyenv` .

### Virtual Environment Setup

Always use a virtual environment to isolate project dependencies. To create and activate a virtual environment using `uv`:

```bash
uv venv .venv  # Creates a virtual environment in the .venv directory
source .venv/bin/activate  # On Linux/macOS
# .venv\Scripts\activate  # On Windows
```

Alternatively, you can specify a Python version:

```bash
uv venv --python 3.12 .venv
```

> **Note:** `uv` will automatically download the specified Python version if it's not already installed on your system .

### Dependency Management

#### Adding Dependencies

To add a new dependency to your project:

```bash
uv add <package-name>
```

This command updates your `pyproject.toml` and `uv.lock` files, ensuring reproducible builds.

For development-only dependencies:

```bash
uv add --dev <package-name>
```

#### Installing Dependencies

To install all dependencies defined in your `pyproject.toml`:

```bash
uv sync
```

This ensures your virtual environment matches the specified dependencies.

#### Removing Dependencies

To remove a dependency:

```bash
uv remove <package-name>
```

This updates your `pyproject.toml` and `uv.lock` accordingly.

#### Freezing Dependencies

To generate a `requirements.txt` file from your current environment:

```bash
uv pip freeze > requirements.txt
```

This can be useful for compatibility with tools that expect a `requirements.txt` file.

### Project Initialization

For new projects, initialize with:

```bash
uv init
```

This sets up a new project with a `pyproject.toml`, `.gitignore`, and `.venv` directory.

### Version Control Considerations

Ensure your `.gitignore` includes the following to avoid committing virtual environments and lock files:

```bash
.venv/
uv.lock
```

Certainly! Here's an updated version of Section 6 that integrates the use of Ruff for import organization, aligning with your project's configuration:

---

## 6. Imports

We adhere to **PEP 8** guidelines for organizing imports:

1. **Standard library imports** (e.g., `os`, `sys`)
2. **Related third-party imports** (e.g., `pandas`, `numpy`, `requests`)
3. **Local application/library-specific imports**

Each group should be separated by a blank line.

### Automating Import Organization with Ruff

With the configurations outlined in Section 2, Ruff automatically handles import sorting and grouping:

* **Import Sorting**: Ruff's linter includes rules (prefixed with `I`) that sort imports into the correct order and group them appropriately. This behavior is similar to `isort` with the `black` profile .

* **Automation**: To apply these rules, run:

  ```bash
  ruff check --fix .
  ```

  This command checks for import sorting issues and automatically fixes them.

* **Configuration**: Ensure your `pyproject.toml` includes the following to enable import sorting:

  ```toml
  [tool.ruff.lint]
  select = ["E", "F", "W", "N", "I", "UP", "B", "C4", "SIM"]
  ```

  The `"I"` code activates import sorting rules.

* **Formatter Note**: Ruff's formatter (`ruff format`) does not sort imports. Import sorting is exclusively handled by the linter component .

### Best Practices

* **Avoid Wildcard Imports**: Do not use `from module import *` as it pollutes the namespace and makes code harder to read and debug.
* **Prefer Absolute Imports**: Use absolute imports (e.g., `from my_package.my_module import my_function`) for clarity. Relative imports can be acceptable within the same package.

## 7. Error Handling & Exceptions

* Handle exceptions gracefully. Don't let your scripts crash without informative messages.
* Be specific in your `except` clauses (e.g., `except FileNotFoundError:` instead of `except Exception:`).
* Use `try...except...else...finally` blocks appropriately.
  * The `else` block runs if no exception occurs in the `try` block.
  * The `finally` block runs no matter what (useful for cleanup).
* Raise custom exceptions when appropriate to provide more context-specific error information.
* **Logging:** Use the `logging` module for application-level logging instead of `print()` statements for debugging or status messages in production/shared code.
* Configure different logging levels (DEBUG, INFO, WARNING, ERROR, CRITICAL).
* Example:

    ```python
    import logging

    logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

    try:
        # ... some operation ...
        logging.info("Operation successful.")
    except ValueError as e:
        logging.error(f"Invalid value encountered: {e}")
        raise # Re-raise the exception if it needs to be handled upstream
    ```

## 8. Modularity, Reusability & Project Structure

* **DRY (Don't Repeat Yourself):** If you find yourself copying and pasting code, consider abstracting it into a function or class.
* **Functions:**
  * Should be small and do one specific thing well.
  * Aim for functions with fewer than 20-30 lines of code (this is a guideline, not a strict rule).
  * Use descriptive names.
* **Classes:**
  * Use classes to group related data and behavior.
  * Follow the Single Responsibility Principle.
* **Project Structure:**
  * Organize your code into logical modules and packages. A typical structure might be:

    ```
    my_project/
    ├── .gitignore             
    ├── .python-version        
    ├── .venv/               
    ├── README.md             
    ├── pyproject.toml         
    ├── uv.lock             
    ├── data/                  
    ├── notebooks/            
    ├── my_project_module/    
    │   ├── __init__.py
    │   ├── module1.py
    │   ├── subpackage/
    │   │   └── __init__.py
    │   └── utils.py
    ├── scripts/              
    └── tests/                
        ├── __init__.py
        └── test_module1.py
    ```

  * Adapt this structure based on project needs.

## 9. Testing

Testing is crucial for ensuring code correctness and enabling safe refactoring.

* **Write Unit Tests:** Test individual functions and methods in isolation.
* **Framework:** We recommend **`pytest`** for its conciseness and powerful features.
  * Installation: `pip install pytest`
  * Create test files named `test_*.py` or `*_test.py`.
  * Write test functions named `test_*()`.
  * Run tests with `pytest` in your terminal.
* **Test Coverage:** Aim for good test coverage, especially for critical business logic and utility functions.
* Place tests in a dedicated `tests/` directory parallel to your main source code directory.

## 10. Things to Generally Avoid

* **Mutable Default Arguments:** (e.g., `def func(a, my_list=[]):`). The list is created once when the function is defined. Use `my_list: Optional[list] = None` and `if my_list is None: my_list = []` inside the function.
* **Overly complex list/dict comprehensions or generator expressions:** Prioritize readability. If it's hard to read, a simple loop might be better.
* **Silent error suppression:** (e.g., `try: ... except: pass`). Always handle or log errors.
* **Circular dependencies:** Structure your modules to avoid them.
* **Large data files in Git:** Use `.gitignore` and consider solutions like Git LFS or cloud storage for large datasets.

## 11. Tooling Summary & Configuration

* **Python:** 3.10+
* **Environments & Packages:** `uv` (use `uv venv`, `uv add`, `uv sync`). Configured via `pyproject.toml` / `uv.lock`.
* **Formatter:** Ruff (`ruff format .`). Configured via `pyproject.toml`.
* **Linter & Import Sorting:** Ruff (`ruff check . --fix` or `ruff .`). Configured via `pyproject.toml`.
* **Type Checking:** IDE-based (e.g., Pylance in VS Code).
* **Testing:** `pytest` (run with `pytest`; install with `uv add --dev pytest`).
* **Key Config File:** `pyproject.toml` for nearly everything. **Must have it.**

---

This is a starting point. Let's review and refine this together!
