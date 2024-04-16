# Python Package Setup Guide

This guide provides step-by-step instructions for setting up a maintainable and extensible Python package using Poetry. It includes guidance on folder structure, adding packages, running scripts, setting up unit tests, and configuring GitHub Actions for continuous integration.

## Poetry Project Setup
Prerequisites:
* Python 3.7 or higher installed
* Poetry installed (https://python-poetry.org/docs/#installation)

### Step 1: Create Your Project
Create the project directory and initialize the project with Poetry.
```bash
mkdir my-package
cd my-package
poetry init
```

### Step 2: Create the Folder Structure
Create the folder structure manually:
```bash
mkdir -p src/hello_app src/bye_app tests
touch src/hello_app/hello_main.py src/bye_app/bye_main.py
touch src/__init__.py src/hello_app/__init__.py src/bye_app/__init__.py
touch tests/__init__.py tests/test_hello_app.py tests/test_bye_app.py 
echo "# Python Package Demo" > README.md
```
Your initial folder structure should look like this:
```
my-package/
│
├── src/                  # Source code directory
│   ├── __init__.py       # Makes src a Python package
│   ├── hello_app/        # Sub-package for the hello app
│   │   ├── __init__.py   # Makes hello_app a Python package
│   │   └── hello_main.py # Main script for the hello app
│   ├── bye_app/          # Sub-package for the bye app
│   │   ├── __init__.py   # Makes bye_app a Python package
│   │   └── bye_main.py   # Main script for the bye app
│
├── tests/                # Test directory
│   ├── __init__.py       # Makes tests a Python package
│   ├── test_hello_app.py # Test file for the hello app
│   └── test_bye_app.py   # Test file for the bye app
│
├── pyproject.toml        # Dependency management and packaging settings
├── README.md
```

### Step 3: Writing Code
Write your `hello_app` and `bye_app` modules in the `src` directory:
- `hello_main.py`
    ```python
    import requests

    def main():
        url = "https://api.github.com"  # Example API endpoint
        response = requests.get(url)
        if response.status_code == 200:
            print("Success! API data received:")
            print(response.json())  # Print JSON response from the API
        else:
            print("Failed to retrieve data.")

    if __name__ == "__main__":
        main()
    ```
- `bye_main.py`
  ```python
  def main():
      print("Goodbye, world!")

  if __name__ == "__main__":
      main()
  ```
### Step 4: Writing Tests
Write tests for your modules in the `tests` directory:
- `test_hello_app.py`
    ```python
    from src.hello_app.hello_main import main as hello_main

    def test_hello():
        assert hello_main() is None  # Example test
    ```
- `test_bye_app.py`
    ```python
    from src.bye_app.bye_main import main as bye_main

    def test_bye():
        assert bye_main() is None  # Example test
    ```
### Step 5: Add Dependencies and Scripts
#### Add a Dependency
```bash
poetry add requests  # Add 'requests' package
```

#### Add Scripts
Modify the `pyproject.toml` to include runnable scripts:
```toml
[tool.poetry]
...
packages = [
    {include = "*", from = "src"}
]

[tool.poetry.scripts]
hello = "hello_app.hello_main:main"
bye = "bye_app.bye_main:main"

[tool.poetry.group.dev.dependencies]
pytest = "^8.1.1"
```
### Step 6: Running the Application
To run the scripts:
```bash
poetry lock 
poetry install  
poetry run hello
poetry run bye
```
### Step 7: Running Tests
Execute tests using:
```bash
poetry add --dev pytest
poetry run pytest
```

This setup ensures your package is maintainable, and you can easily manage dependencies, test your code, and add more modules or scripts as needed.

## Git Setup
### Step 1: Configure the .gitignore File
Create a `.gitignore` file in the root of your project to ignore unnecessary files:
```plaintext
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
*.egg-info/
.installed.cfg
*.egg

# Pytest
.cache/
.pytest_cache/

# Pyenv
.python-version

# Poetry
.poetry-env/

# IDEs and editors
.idea/
.vscode/
*.swp
```

### Step 2: Configure GitHub Actions
Create a file `.github/workflows/python-package.yml`:
```bash
mkdir -p .github/workflows && touch .github/workflows/python-package.yml
```
```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: '3.x'
    - name: Install Poetry
      run: |
        curl -sSL https://install.python-poetry.org | python3 -

    - name: Install dependencies using Poetry
      run: |
        poetry install

    - name: Run tests with pytest using Poetry
      run: |
        poetry run pytest tests
```
### Step 3: Initialize Git Repository
Initialize a Git repository to manage your version control:
```bash
git init
git remote add origin [URL-of-your-git-repository]
git add .
git commit -m "Initial commit"
git push --set-upstream origin main
```
