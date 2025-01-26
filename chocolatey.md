# Chocolatey

Chocolatey is a Windows package manager that simplifies installing, updating, and managing software and tools from the command line.

## Install .NET

[.NET Framework Runtime](https://dotnet.microsoft.com/en-us/download/dotnet-framework)

## Install Chocolatey

1. Go to [Chocolatey](https://chocolatey.org/install).
2. Follow the instructions for 'Install Chocolatey for Individual Use'.

In PowerShell as admin: 

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

choco --version
```

## Update Chocolatey

In PowerShell as admin: 

```powershell
choco --version
choco upgrade chocolatey
choco --version
```
