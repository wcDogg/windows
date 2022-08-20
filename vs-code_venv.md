# Python Virtual Environment in VS Code

## Git for Windows

The easiest way to run Python in a VS Code Virtual Environment is with the GitBash terminal included with Git for Windows. 

See: [Git + GitHub for Windows.](https://github.com/CornDoggSoup/windows/blob/main/git-github.md)

Note that as of 2022-06-01, Git Bash and Command Prompt terminals prefix commands with `&` instead of `source`. This is a VS Code issue that is being fixed but has not been released. Issue does not affect PowerShell terminal. 

The workaround is given here: (microsoft/vscode-python/issues/16175)[https://github.com/microsoft/vscode-python/issues/16175]. In short, add this to your `settings.json`:

```
    "terminal.integrated.defaultProfile.windows": "Git Bash",
    "terminal.integrated.profiles.windows": {
        "Git Bash": {
            "source": "Git Bash",
            "icon": "terminal-bash"
        }
    },
    "terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
```

## VS Code Extensions

Install these extensions. Exit VS Code.

* Microsoft Python
* Microsoft Pylance

## Create a Virtual Environment

Open project folder in VS Code. Start a new Bash terminal.

```
python -m venv venv
venv/Scripts/activate.bat

# Command Palette > Select Interpreter > Enter path
# Browse to and select `project\venv\Scripts\python.exe`
# Kill / start new terminal. May need to restart VS Code.

# Prompt now shows (venv). Do a sanity check. 
which pip

# Install requirements.
python -m pip install -r requirements.txt --upgrade

# At the very least:
python -m pip install --upgrade pip setuptools wheel
```

## requirements.txt

[How to specify requirements](https://pip.pypa.io/en/stable/reference/requirements-file-format/)

This is a simple `requirements.txt` - it goes in the project root:

```
pip
setuptools
wheel
python-decouple
requests
# pytz
# appdirs
# Pillow
```
Install everything in this file: 

```
py -m pip install -r requirements.txt --upgrade
```

## Issue: `Import module could not be resolved from source Pylance`

Reason: Modules are installing globally instead of in the venv. Check:

* `which pip3` points to `venv/Scripts/pip3`.
* `venv/Lib` for the installed packages.

Reason: No empty `__init__.py` in root + subfolders. 

* Add `__init__.py`. 
