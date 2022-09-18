# Python Virtual Environment in VS Code


## Git for Windows

The easiest way to run Python in a VS Code Virtual Environment is with the Git Bash terminal included with Git for Windows. 

* See: [Git + GitHub for Windows.](git-github.md)


## settings.json

Note that as of 2022-06-01, Git Bash and Command Prompt terminals prefix commands with `&` instead of `source`. This is a VS Code issue that is being fixed but has not been released. Issue does not affect PowerShell terminal. 

The workaround is given here: (microsoft/vscode-python/issues/16175)[https://github.com/microsoft/vscode-python/issues/16175]. In short, modify your `settings.json`.

1. Open VS Code.
2. In the bottom-left corner, click the gear icon > Settings.
3. In the upper-right corner, click the **Open settings.json** icon (file).
4. Add the following lines and save:

```json
    "terminal.integrated.profiles.windows": {
        "Git Bash": {
            "path": "C:\\Program Files\\Git\\bin\\bash.exe",
            "source": "Git Bash",
            "args": [],
            "icon": "terminal-bash"
          },
    },
    "terminal.integrated.defaultProfile.windows": "Git Bash",
```

## VS Code Extensions

Install these extensions. 

* Microsoft Python
* Microsoft Pylance


## Create a Virtual Environment

VS Code venv can be a little finicky to start. These are the steps I follow. Once configured, the environment will automatically activate any time you open the project directory in VS Code.

1. Open project folder in VS Code.
2. Use `Ctrl+Shift+~` to open the terminal.
3. In the upper-right corner, use the New Terminal icon (+) to open a bash shell if needed.
   

```bash
# Create / open a .py file

# Create venv - this takes a minute 
# Adds a venv folder to project
python -m venv venv

# Activate - there is no visual confirmation
venv/Scripts/activate.bat

# Command Palette > Python: Select Interpreter > Enter path
# Browse to and select `project\venv\Scripts\python.exe`
# Kill / start new terminal. May need to restart VS Code.

# Prompt now shows (venv). Do a sanity check. 
which pip

# Update
python -m pip install --upgrade pip setuptools wheel

# Use requirements.txt
# https://pip.pypa.io/en/stable/reference/requirements-file-format/
python -m pip install -r requirements.txt --upgrade
```


## Issue: `Import module could not be resolved from source Pylance`

Reason: Modules are installing globally instead of in the venv. Check:

* `which pip3` points to `venv/Scripts/pip3`.
* `venv/Lib` for the installed packages.

Reason: No empty `__init__.py` in root + subfolders. 

* Add `__init__.py`. 
