# PowerShell 7


## Install PowerShell 7

Be sure to check the options for adding to `$PATH` and VS Code.

* GitHub: [PowerShell](https://github.com/PowerShell/PowerShell)
* Microsoft: [Install PowerShell on Windows, Linux, and macOS](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell?view=powershell-7.2)


## Disable Telemetry

* Microsoft: [PowerShell Telemetry](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_telemetry?view=powershell-7.2)

You can skip this and add it to a profile later. 

```powershell
$env:POWERSHELL_TELEMETRY_OPTOUT
```
  
## Set Execution Policy

This is a one-time step that cannot be done via a profile. In PS as admin:

```powershell
$value = Get-ExecutionPolicy
if ($value -eq 'RemoteSigned') {
  Write-Host "Execution policy is currently 'RemoteSigned'. No changes made."
} else {
  Set-ExecutionPolicy RemoteSigned
  Write-Host "Execution policy changed to 'RemoteSigned'."
}
```

## About PowerShell Profiles

At some point you'll want or need to create profiles. I found it best to use 2 profiles - 1 for all users and one for me as the current user. 

* Micorsoft: [PowerShell Profiles](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.2#the-profile-files)

To create and open profiles, from PowerShell as admin:

```powershell
# All users, all hosts
Code $profile.AllUsersAllHosts
notepad $profile.AllUsersAllHosts

# Current user, all hosts
Code $Home\Documents\PowerShell\Profile.ps1
notepad $Home\Documents\PowerShell\Profile.ps1
```

## All Users, All Hosts Profile

* The variable for this file is: `$PSHOME\Profile.ps1`.
* The command to locate this file is: `$profile.AllUsersAllHosts`.

In PowerShell as an admin, test if this file exists and create if needed:

```powershell
if (Test-Path $profile.AllUsersAllHosts) {
  Write-Host "Profile exists."
} else {
  New-Item -ItemType File -Path $profile.AllUsersAllHosts -Force
  Write-Host "Profile created."  
}
```

Use this profile to share settings - like starting the OpenSSH server - across users. For example:

```ps1
# Variables
$env:POWERSHELL_TELEMETRY_OPTOUT = 1
$VerbosePreference = "SilentlyContinue"

# Oh My Posh
oh-my-posh init pwsh --config ~/.default.omp.json | Invoke-Expression

# SSH Agent
$sshAgentStopped = 'Stopped' -eq (Get-Service -Name 'ssh-agent' -ErrorAction SilentlyContinue).status

if ($sshAgentStopped) {
  Write-Host "Starting SSH Agent..."
  Start-Service -Name 'ssh-agent'
  Write-Host "SSH Agent is running."
  Get-Service ssh-agent
} else {
  Write-Host "SSH Agent is running."
  #Get-Service ssh-agent
}

# Open this profile in VS Code
function ProAllCode {Code $PROFILE.AllUsersAllHosts}

# Open this profile in Notepad
function ProAllNP {notepad $PROFILE.AllUsersAllHosts}
```

## Current User, All Hosts Profile
 
* The variable for this profile is: `$PROFILE.CurrentUserAllHosts`.
* The command to locate this file is:`$Home\Documents\PowerShell\Profile.ps1`

In PowerShell test if this file exists and create if needed:

```powershell
if (Test-Path $Home\Documents\PowerShell\Profile.ps1) {
  Write-Host "Profile exists."
} else {
  New-Item -ItemType File -Path $Home\Documents\PowerShell\Profile.ps1 -Force
  Write-Host "Profile created."  
}
```

Use this profile for user-specific settings like SSH to remote servers.

```ps1
# SSH to NAS
function NAS {ssh user@192.168.1.xxx -pxxxxx}

# Open this profile in VS Code
function ProMeCode {Code $Home\Documents\PowerShell\Profile.ps1}

# Open this profile in Notepad
function ProMeNP {notepad $Home\Documents\PowerShell\Profile.ps1}
```


## Learn More

* GitHub: [Learn PowerShell](https://github.com/PowerShell/PowerShell/tree/master/docs/learning-powershell)
* Microsoft: [Migrating from Windows PowerShell 5.1 to PowerShell 7](https://docs.microsoft.com/en-us/powershell/scripting/whats-new/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7.2)
* Microsoft: [About Prompts](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_prompts?view=powershell-7.2)
* [PSHostRawUserInterface Class](https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.host.pshostrawuserinterface?view=powershellsdk-7.0.0) 
* [GNU Bash](https://www.gnu.org/software/bash/)
* mohitgoyal.co: [Write Verbose Output in PowerShell using Write-Verbose](https://mohitgoyal.co/2019/07/05/write-verbose-output-in-powershell-using-write-verbose/)

