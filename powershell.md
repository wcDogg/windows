# Microsoft PowerShell 7

How to install Microsoft PowerShell 7 and create user profiles. 

## 1. Install PowerShell 7

Microsoft: [Install PowerShell on Windows, Linux, and macOS](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell?view=powershell-7.2)

- By default, the installer installs the package in `$Env:ProgramFiles\PowerShell\7`. 
- The installed location is added to your `$Env:PATH` environment variable.

```powershell
# Search for latest version
winget search --id Microsoft.PowerShell --exact

# Install default MSIX package
winget install --id Microsoft.PowerShell --source winget

# Check version
pwsh -version

```

## 2. Set Execution Policy

This is a one-time step that cannot be done via a profile. In PS7 as admin:

```powershell
$value = Get-ExecutionPolicy
if ($value -eq 'RemoteSigned') {
  Write-Host "Execution policy is currently 'RemoteSigned'. No changes made."
} else {
  Set-ExecutionPolicy RemoteSigned
  Write-Host "Execution policy changed to 'RemoteSigned'."
}
```

Alternatively, in the Windows UI: 

1. Windows > Settings > System
2. Advanced > Terminal: PowerShell
3. `Change execution policy` = ON


## 3. Set Default Terminal Profile

These steps set PowerShell as the default shell profile for apps like VS Code.

1. Open Windows Terminal (Terminal app).
2. In the title bar, select the down arrow > Settings - a new tab opens in the Terminal.
3. In the left panel, select `Startup`.
4. Set `Default Profile` to `PowerShell`. Click `Save`.


## 4. Install Oh My Posh

See: [Oh My Posh](./oh-my-posh.md)


## 5. Configure Font

These steps configure the default font used for all shells. 

1. Open Windows Terminal (Terminal app).
2. In the title bar, select the down arrow > Settings - a new tab opens in the Terminal.
3. In the left panel, select `Profiles > Default`.
4. On this screen, scroll down to `Additional Settings` and select `Appearance`. 
5. Adjust `Font Face` and `Font Size`. Click `Save`. 

Test by restarting PowerShell as a non-admin.


## 4. Update Profile

Microsoft: [PowerShell Profiles](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.2#the-profile-files)

```powershell
# PowerShell 7 as Admin
# Get location of profile
$PROFILE

# Open in VS Code to edit
Code $PROFILE

# 
# Add this to profile and save.
#

# Variables
$env:POWERSHELL_TELEMETRY_OPTOUT = 1
$VerbosePreference = "SilentlyContinue"

# Command History
Set-PSReadlineOption -HistoryNoDuplicates

# Oh My Posh
oh-my-posh init pwsh --config ~/aliens.omp.json | Invoke-Expression

```

