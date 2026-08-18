# What Is Python ?

Python is a popular, high-level, and versatile programming language known for its readability and beginner-friendliness. It is used for a wide range of applications, including web development, data science, artificial intelligence, and scripting. Developed by Guido van Rossum and first released in 1991, it is now maintained by the Python Software Foundation.

In order to run Python you will need to install it from the [python](https://www.python.org/downloads) website. The python package manager is `pip`.

---

## Python Virtual Environment

A Python virtual environment `venv` is an isolated workspace that lets a project use its own dependencies without affecting other projects or the global Python installation. It helps keep projects clean, reproducible, and free from version conflicts.

To create a virtual environment in VS Code.
- Step 1: Open your project folder in VS Code.
- Step 2: Press `Ctrl + Shift + P`.
- Step 3: Type `Python: Create Environment`
- Step 4: Select `Venv` and choose your Python version.

VS Code will:
- Create the folder `venv/.`
- Activate it.
- Set it as the interpreter automatically.

Or you can also manually do it in your terminal.
```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

---