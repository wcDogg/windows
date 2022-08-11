# Chocolatey

* [.NET Framework Runtime](https://dotnet.microsoft.com/en-us/download/dotnet-framework)
* [Chocolatey](https://chocolatey.org/install)

```
# PS as admin

Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

choco --version
```

TODO: add tab completetion to PS profile.
```
WARNING: Not setting tab completion: Profile file does not exist at 'C:\Users\wcd\Documents\PowerShell\Microsoft.PowerShell_profile.ps1'.
```
