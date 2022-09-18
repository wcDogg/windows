# VS Code

## Install

[Microsoft VS Code](https://code.visualstudio.com/)


## settings.json

This is an example of my VS Code `settings.json`. 

1. Open VS Code.
2. In the bottom-left, click the Gear icon > Settings.
3. In the upper-right corner, click the **Open settings.json** icon (file).

```
{
    "telemetry.telemetryLevel": "crash",
    "security.workspace.trust.untrustedFiles": "open",
    "git.ignoreMissingGitWarning": true,
    "git.confirmSync": false,
    "git.autofetch": true,
    "git.enableSmartCommit": true,
    "files.autoSave": "onFocusChange",
    "editor.tabSize": 2,
    "editor.wordWrap": "on",
    "editor.fontSize": 16,
    "editor.fontWeight": "normal",
    "editor.fontFamily": "'Comic Code2', Consolas, 'Courier New', monospace",
    "editor.fontLigatures": false,
    "terminal.integrated.defaultProfile.windows": "Git Bash",
    "terminal.integrated.profiles.windows": {
        "Git Bash": {
            "path": ["C:\\Program Files\\Git\\bin\\bash.exe"],
            "source": "Git Bash",
            "icon": "terminal-bash"
        },
        "PowerShell": { 
            "path": ["C:\\Programs\\PowerShell\\pwsh.exe"], 
            "source": "PowerShell", 
            "args": [], 
            "icon": "terminal-powershell" 
        },
    },
    "terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
    "terminal.integrated.fontSize": 16,
    "terminal.integrated.fontWeight": "normal",
    "terminal.integrated.fontFamily": "'CaskaydiaCove NF', Consolas, 'Courier New', monospace",
    "terminal.integrated.copyOnSelection": true,
    "files.eol": "\n",
    "[html]": {
        "editor.defaultFormatter": "mohd-akram.vscode-html-format"
    }
}
```
