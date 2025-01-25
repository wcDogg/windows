# Chocolatey

## Install .NET

[.NET Framework Runtime](https://dotnet.microsoft.com/en-us/download/dotnet-framework)


## Install Chocolatey

[Chocolatey](https://chocolatey.org/install)

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
