# General Python Development Best Practices

This document outlines key best practices for Python development, designed to help maintain clean, manageable, and secure code. These practices are particularly relevant for projects like `adk-voice-agent`.

## 1. Python Version Management

Different projects may require different Python versions. Managing these versions effectively prevents conflicts and ensures reproducibility.

*   **Tools:**
    *   **`pyenv`**: Allows you to easily switch between multiple Python versions on your system. It can set global, local (per-project), or shell-specific Python versions.
        *   *Benefit:* Isolates projects needing different Python patch versions or even major versions (e.g., Python 3.9 vs. Python 3.10).
    *   **`conda`** (from Anaconda/Miniconda): A package, dependency, and environment manager. While often used for data science, it's also excellent for managing Python versions and complex non-Python dependencies.
        *   *Benefit:* Creates entirely isolated environments that can have their own Python version and packages, separate from system Python.

*   **Why it matters:**
    *   Ensures your development environment matches the deployment environment.
    *   Avoids "it works on my machine" problems related to Python version differences.
    *   Allows testing against multiple Python versions.

## 2. Virtual Environments

Virtual environments are crucial for isolating project-specific dependencies and avoiding conflicts between projects.

*   **Tool: `venv`** (built into Python 3.3+)
    *   **Creation:**
        ```bash
        python3 -m venv .venv # Creates a virtual environment in a .venv folder
        ```
    *   **Activation:**
        *   macOS/Linux: `source .venv/bin/activate`
        *   Windows: `.venv\Scripts\activate`
    *   **Deactivation:** `deactivate` (once activated)

*   **`conda` environments:**
    *   **Creation:** `conda create -n myenv python=3.9` (creates an environment named `myenv` with Python 3.9)
    *   **Activation:** `conda activate myenv`
    *   **Deactivation:** `conda deactivate`

*   **Why they are crucial:**
    *   **Dependency Isolation:** Each project can have its own set of dependencies and versions, preventing conflicts (e.g., Project A needs `requests==2.20.0`, Project B needs `requests==2.25.0`).
    *   **Clean Global Python:** Keeps your system's global Python installation clean and free from project-specific packages.
    *   **Reproducibility:** Makes it easier to replicate the development environment elsewhere.

*   **`.gitignore`:** Always add your virtual environment directory to `.gitignore` (e.g., `.venv/`, `env/`). These directories can be large and are specific to your local machine.

## 3. Dependency Management

Properly managing project dependencies is key to collaboration and deployment.

*   **`requirements.txt`:** A standard file for listing project dependencies.
    *   **Generating:** After installing necessary packages in your activated virtual environment:
        ```bash
        pip freeze > requirements.txt
        ```
    *   **Installing:** To install dependencies on a new setup or for another developer:
        ```bash
        pip install -r requirements.txt
        ```

*   **Best Practices for `requirements.txt`:**
    *   **Keep it updated:** Regenerate it whenever you add, remove, or update a dependency.
    *   **Be specific (usually):** `pip freeze` lists exact versions (e.g., `package_name==1.2.3`). This is good for application reproducibility. For libraries, you might use more flexible versioning (e.g., `package_name>=1.2.0`).
    *   **One package per line.**
    *   **Comment for clarity:** You can add comments (`# This is for X feature`) if needed.
    *   **Consider tools like `pip-tools`** (which uses `requirements.in` and `requirements.txt`) for more advanced dependency management, especially pinning transitive dependencies.

## 4. API Key and Secret Management

**Never hardcode API keys, passwords, or other secrets directly into your source code.**

*   **Environment Variables:**
    *   Store secrets as environment variables on the system where the code runs.
    *   Access them in Python using `os.getenv()`:
        ```python
        import os
        api_key = os.getenv("MY_API_KEY")
        if not api_key:
            raise ValueError("MY_API_KEY environment variable not set.")
        ```

*   **`.env` Files:**
    *   For local development, use a `.env` file to store environment variables. This file should **never** be committed to Git.
    *   Create a `.env.example` file (committed to Git) that lists the required variables without their actual values.
        ```
        # .env.example
        GOOGLE_API_KEY=your_google_api_key_here
        OPENAI_API_KEY=your_openai_api_key_here
        ```
    *   Add `.env` to your `.gitignore` file.
    *   Use a library like `python-dotenv` to load variables from `.env` automatically during development:
        ```python
        from dotenv import load_dotenv
        load_dotenv() # Loads variables from .env into the environment
        import os
        api_key = os.getenv("MY_API_KEY")
        ```

*   **Platform-Specific Secret Management:**
    *   Cloud platforms (AWS, Google Cloud, Azure) and CI/CD systems (GitHub Actions, GitLab CI, Jules) provide secure ways to store and inject secrets into your application environment at runtime. **These are the preferred methods for production and sensitive environments.**
    *   For Jules, use its built-in secret management features.

*   **Secure Handling of Credential Files:**
    *   Files like `credentials.json` (Google OAuth client secrets) or `token.json` (generated OAuth tokens) contain sensitive information.
    *   **`credentials.json`**:
        *   Ideally, the *contents* (client ID, client secret) should be stored as individual secrets using your platform's secret manager or as environment variables.
        *   If the file itself is used, ensure it's not in Git and is securely managed.
    *   **`token.json`**:
        *   This file is generated after a user authorizes the application and contains refresh tokens. **It absolutely must not be committed to Git.**
        *   Store its contents securely, for example, by uploading its JSON content as a secret in Jules, or storing it in a secure, encrypted location. Your application then reconstructs it or uses its values at runtime.
    *   Always add these filenames to `.gitignore`.

## 5. Code Formatting and Linting

Consistent code style and automated checks for common errors improve readability, maintainability, and collaboration.

*   **`Black` (Code Formatter):**
    *   An opinionated code formatter that automatically reformats your Python code to a consistent style.
    *   *Benefit:* Eliminates debates about style; code looks the same regardless of who wrote it.
    *   *Usage:*
        ```bash
        pip install black
        black . # Formats all .py files in the current directory and subdirectories
        black your_file.py # Formats a specific file
        ```

*   **`Flake8` (Linter):**
    *   A wrapper around PyFlakes, pycodestyle, and McCabe. It checks for:
        *   Logical errors (e.g., unused variables, undefined names).
        *   Style violations (PEP 8).
        *   Code complexity.
    *   *Benefit:* Catches potential bugs and enforces coding standards.
    *   *Usage:*
        ```bash
        pip install flake8
        flake8 . # Lints all .py files in the current directory and subdirectories
        flake8 your_file.py # Lints a specific file
        ```

*   **`pre-commit` Hooks:**
    *   A framework for managing and maintaining multi-language pre-commit hooks.
    *   *Benefit:* Automates running formatters (like Black) and linters (like Flake8) every time you try to commit code. This ensures that only clean, well-formatted code gets committed.
    *   *Setup:*
        1.  `pip install pre-commit`
        2.  Create a `.pre-commit-config.yaml` file in your repository root:
            ```yaml
            repos:
            -   repo: https://github.com/psf/black
                rev: 23.11.0 # Use a recent stable version
                hooks:
                -   id: black
            -   repo: https://github.com/PyCQA/flake8
                rev: 6.1.0 # Use a recent stable version
                hooks:
                -   id: flake8
            ```
        3.  Install the hooks: `pre-commit install`
    *   Now, `black` and `flake8` will run automatically on changed files before each commit.

## 6. `.gitignore` File

A `.gitignore` file tells Git which files or directories to ignore in a project. This is crucial for keeping your repository clean and avoiding accidental commits of sensitive or unnecessary files.

*   **Purpose:**
    *   Prevents committing generated files (e.g., compiled code, virtual environments).
    *   Prevents committing sensitive information (e.g., `.env` files, `token.json`).
    *   Keeps the repository focused on source code and essential project files.

*   **Comprehensive Python `.gitignore` Example:**
    ```gitignore
    # Byte-compiled / optimized / DLL files
    __pycache__/
    *.py[cod]
    *$py.class

    # C extensions
    *.so

    # Distribution / packaging
    .Python
    build/
    develop-eggs/
    dist/
    downloads/
    eggs/
    .eggs/
    lib/
    lib64/
    parts/
    sdist/
    var/
    wheels/
    pip-wheel-metadata/
    share/python-wheels/
    *.egg-info/
    .installed.cfg
    *.egg
    MANIFEST

    # PyInstaller
    # Usually these files are written by a PyInstaller script; this is targeted towards
    # PyInstaller default files in development mode.
    *.manifest
    *.spec

    # Installer logs
    pip-log.txt
    pip-delete-this-directory.txt

    # Unit test / coverage reports
    htmlcov/
    .tox/
    .nox/
    .coverage
    .coverage.*
    .cache
    nosetests.xml
    coverage.xml
    *.cover
    *.py,cover
    .hypothesis/
    .pytest_cache/

    # Translations
    *.mo
    *.pot
    *.log

    # Django stuff:
    *.log
    local_settings.py
    db.sqlite3
    db.sqlite3-journal

    # Flask stuff:
    instance/
    .webassets-cache

    # Scrapy stuff:
    .scrapy

    # Sphinx documentation
    docs/_build/

    # Jupyter Notebook
    .ipynb_checkpoints

    # IPython
    profile_default/
    ipython_config.py

    # pyenv
    .python-version

    # pipenv
    # According to PDM PEP 582 recommendation, this should be
    # the default relative storage location.
    .pdm.toml
    .pdm.lock
    .pdm-python

    # PEP 582; used by PDM, Flit and potentially other tools.
    __pypackages__/

    # Celery stuff
    celerybeat-schedule
    celerybeat.pid

    # SageMath parsed files
    *.sage.py

    # Environments
    .env
    .venv
    env/
    venv/
    ENV/
    env.bak/
    venv.bak/

    # Spyder project settings
    .spyderproject
    .spyproject

    # Rope project settings
    .ropeproject

    # mkdocs documentation
    /site

    # mypy
    .mypy_cache/
    .dmypy.json
    dmypy.json

    # Pyre type checker
    .pyre/

    # pytype static analyzer
    .pytype/

    # Cython debug symbols
    cython_debug/

    # Secret files
    credentials.json
    token.json
    *.pem
    *.key

    # IDE / Editor specific files
    .vscode/
    .idea/
    *.sublime-project
    *.sublime-workspace
    *.DS_Store
    *.atom/
    .project
    .settings/
    *.tmproj
    *.bak
    *.swp
    *~
    ```

By adhering to these best practices, you can create more robust, maintainable, and secure Python applications.
```
