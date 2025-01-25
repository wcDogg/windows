# OpenSSH on Windows 11 

Logging into remote services with a username & password is a PITA and it's vulnerable to attacks.

OpenSSH uses the SSH protocol for remote login. It uses key pairs for authentication and to encrypt all traffic between client and server. Built-in SSH has always been a Linux thing. Windows 10 and higher also supports OpenSSH. Git and other popular services support & recommend SSH. 

## OpenSSH, VS Code & GitHub

I originally set up OpenSSH because I was working with a NAS and with AWS. These days, I'm working in VS Code and with GitHub. I have 2 different GitHub accounts. 

I'm not a GitHub guru. My primary need is to make GitHub commits using the VS Code UI. Setting up for 2 GitHub accounts was a bit different than setting up for 2 servers. 

By following the steps in this topic and in [Git + GitHub](./git-github.md ), VS Code will automatically detect which SSH keys go with which repositories and will silently handle the authentication. 

## Scenario

* I run Windows 11 Pro. My OS is fully updated.
* I am the only user on my PC and I'm an admin user. 
* I am running PowerShell 7 as an admin. 

## Confirm User and Machine Names

In later steps you will access your computer's OpenSSH Server as `username@servername`. For my scenario: 

* `username` is my computer username `wcd`. 
* `servername` is my PC's name `wcdPC`. 
* `wcd@wcdPC` will be my login for my PC's OpenSSH Server.

You can use these to confirm: 

```powershell
$env:USERNAME
[System.Net.Dns]::GetHostName()
```

## Ensure the Windows Update Service is Running

When you install OpenSSH, it downloads components from Microsoft servers. To do this, the Windows Update service must be running. 

1. Win + R > services.msc
2. Locate the 'Windows Update' service and ensure it is 'Running'.
3. If not, right-click and select 'Start'. 

## Install OpenSSH Client & Server

In PowerShell as an admin: 

```powershell
# Check if OpenSSH is already installed
ssh -V

# If you see a version number, check the Client and Server:
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

Name  : OpenSSH.Client~~~~0.0.1.0
State : Installed

Name  : OpenSSH.Server~~~~0.0.1.0
State : Installed

# If you see 'The term 'ssh' is not recognized..',
# then install the Client and Server. 
# Note: These can take several minutes (5+) to complete. 
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

Path          :
Online        : True
RestartNeeded : False

Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0

Path          :
Online        : True
RestartNeeded : False

# Confirm new installation
ssh -V
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

# Check that a log was created
Get-WinEvent -LogName Application | Where-Object {$_.Message -like '*sshd*'} | Select-Object TimeCreated, Message -Last 5
```

## Set Service Permissions and Start

This step ensures OpenSSH services have the right permissions. You also go here to set how these services start. For right now, use 'Manual' start.

1. Win + R > services.msc
2. Locate and double-click on 'OpenSSH Authentication Agent'
    1. General tab > Startup Type = Manual
    2. Login tab > Use Local System account. Enable 'Allow services to interact with the desktop'
    3. Apply
3. Repeat for 'OpenSSH SSH Server'.


## Configure Firewall Rule

* Installing OpenSSH Server should create & enable a firewall rule named 'OpenSSH-Server-In-TCP'.
* This rule allows inbound SSH traffic on port 22. 
* If this rule is not enabled and this port is not open, connections will be refused or reset. 
* I run Windows Defender Firewall. For me, this rule was set and works as expected. 

```powershell
# Check firewall and add rule if needed
if (Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled) {
    Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' already exists. No changes made."
} else {
    Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
    New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
    Write-Output "Firewall rule created."
}

# View the firewall rule
Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" 

Name                          : OpenSSH-Server-In-TCP
DisplayName                   : OpenSSH SSH Server (sshd)
Description                   : Inbound rule for OpenSSH SSH Server (sshd)
DisplayGroup                  : OpenSSH Server
Group                         : OpenSSH Server
Enabled                       : True
Profile                       : Private
Platform                      : {}
Direction                     : Inbound
Action                        : Allow
EdgeTraversalPolicy           : Block
LooseSourceMapping            : False
LocalOnlyMapping              : False
Owner                         :
PrimaryStatus                 : OK
Status                        : The rule was parsed successfully from the store. (65536)
EnforcementStatus             : NotApplicable
PolicyStoreSource             : PersistentStore
PolicyStoreSourceType         : Local
RemoteDynamicKeywordAddresses : {}
PolicyAppId                   :
PackageFamilyName             :
```




## Check Host Key Permissions

When you install OpenSSH, it creates a `C:\ProgramData\ssh\` directory with `ssh_host_*` keys. 

While usually not necessary, it's a good idea to ensure the NETWORK SERVICE account has read (R) permissions for these keys. 

```powershell
# Check for: NT AUTHORITY\NETWORK SERVICE:(R)
icacls "C:\ProgramData\ssh\ssh_host_*"

# Displays a list of keys like this. (F) indicates the wrong permissions.
C:\ProgramData\ssh\ssh_host_dsa_key BUILTIN\Administrators:(F)
                                    NT AUTHORITY\SYSTEM:(F)

# Fix NT AUTHORITY\SYSTEM:(F) by granting
# read access to the NETWORK SERVICE account
icacls "C:\ProgramData\ssh\ssh_host_*" /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

# Restart PowerShell as admin and check again.
icacls "C:\ProgramData\ssh\ssh_host_*"

# This is correct: 
C:\ProgramData\ssh\ssh_host_dsa_key NT AUTHORITY\NETWORK SERVICE:(R)
                                    BUILTIN\Administrators:(F)
                                    NT AUTHORITY\SYSTEM:(F)

# Restart the SSH Server if needed
Restart-Service -Name sshd
Get-Service -Name sshd

# Check the logs
Get-Content -Path "C:\ProgramData\ssh\logs\sshd.log" -Tail 20

Get-Content: Cannot find path 'C:\ProgramData\ssh\logs\sshd.log' because it does not exist.

# Manually create the log directory
mkdir "C:\ProgramData\ssh\logs"

New-Item: An item with the specified name C:\ProgramData\ssh\logs already exists.

# Grant NETWORK SERVICE account permission to write
icacls "C:\ProgramData\ssh\logs" /grant "NT AUTHORITY\NETWORK SERVICE:(R,W)"

processed file: C:\ProgramData\ssh\logs
Successfully processed 1 files; Failed processing 0 files

# Restart Service (not needed, as we haven't started the service yet)
Restart-Service -Name sshd
```

## Start and Connect to the OpenSSH Server

Some of the remaining steps in this topic require you to connect to the SSH Server. These steps should be done in PowerShell as an admin. 

For this step you will need your logon from **Confirm User and Machine Names** and your Windows password. 

```powershell
# Manually start the server
Start-Service sshd

# Log in
ssh wcd@wcdPC

# The first time you will see:

"The authenticity of host 'wcdpc (::1)' can't be established.
ED25519 key fingerprint is SHA256:FXUtdLzpls1AGDUFSm/OvN7DlO4sLcQRnhw+LsMVH1c.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])?"

# Type 'yes' and you will see:

"Warning: Permanently added 'wcdpc' (ED25519) to the list of known hosts.
Connection reset by ::1 port 22"

# Then log in again: 
ssh wcd@wcdPC

# You know this step worked if the screen refreshes and you see:

Microsoft Windows [Version 10.0.26100.2894]
(c) Microsoft Corporation. All rights reserved.

wcd@WCDPC C:\Users\wcd>


# To disconnect from the server:
exit
```


## Start the SSH Agent

Some of the remaining steps in this topic require you to start the SSH Agent. These steps should be done in PowerShell as an Admin. 

```powershell
# Start the Agent
Start-Service ssh-agent

# Confirm service
Get-Service ssh-agent

Status   Name               DisplayName
------   ----               -----------
Running  ssh-agent          OpenSSH Authentication Agent
```

## Generate a Key Pair

You do NOT have to be connected to the SSH Server or have the SSH Agent running for this step. 

Before starting: 

1. Identify the encryption algorithm needed - for GitHub it's Ed25519.
2. Decide a file name - `id_github_wcd`.
3. Decide a passphrase to encrypt private key.
4. Prepare a password manager entry. Use this to store the key's passphrase and SHA256 fingerprint.

To generate a key pair: 

1. Start the SSH Agent (see above).
2. Use the command below. Follow prompts to set file and passphrase.
3. Create file in the default `C:\Users\wcd\.ssh/` folder. Note the `/`.
4. The result is a public key, a private key, and a SHA256 fingerprint + randomart.  
5. Add passphrase and SHA256 fingerprint to a password manager. 

```powershell
# Generate
ssh-keygen -t ed25519

"Generating public/private ed25519 key pair."
Enter file in which to save the key (C:\Users\wcd/.ssh/id_ed25519): C:\Users\wcd\.ssh/id_github_wcd
Enter passphrase (empty for no passphrase): (will appear blank)
Enter same passphrase again:
"Your identification has been saved in C:\Users\wcd\.ssh/id_github_wcd"
"Your public key has been saved in C:\Users\wcd\.ssh/id_github_wcd.pub"
```

## Add Private Keys to the SSH Agent

This step adds a private key to the SSH Agent for easier management.

For this step:

* The SSH Agent must be running.
* You will need the name of the key and its passphrase.

```powershell
# Start the Agent
Start-Service ssh-agent

# Add a key
ssh-add C:\Users\wcd\.ssh\id_github_wcd

"Identity added: C:\Users\wcd\.ssh\id_github_wcd (wcd@wcdPC)"

# List all keys
ssh-add -l

256 SHA256:IeD...9k wcd@wcdPC (ED25519)
256 SHA256:rI+...oY wcd@wcdPC (ED25519)

# Check permissions
# As a non-admin in PowerShell
Get-Acl C:\Users\wcd\.ssh\id_github_wcd | Format-List
Get-Acl C:\Users\wcd\.ssh\id_github_swatchpie | Format-List

# All 3 of these should be 'FullControl'
Access : NT AUTHORITY\SYSTEM Allow  FullControl
         BUILTIN\Administrators Allow  FullControl
         wcdPC\wcd Allow  FullControl

```

## Add Public Key to SSH Server

To use the key pair, the contents of the public key need to be placed into a text file on the OpenSSH server. 

* For admin users, the contents of the public key must be securely copied to: `C:\ProgramData\ssh\administrators_authorized_keys`. 
* See the Microsoft resources for standard users and multiple admin users. 
* To copy we use `scp` - a secure file-transfer utility provided by the OpenSSH client. 
* The ACL on this file must restrict access to administrators and System.


```powershell
# As an admin in PS, log in to the SSH server
ssh wcd@wcdPC

# Check for / create .ssh directory for current user.
mkdir C:\ProgramData\ssh\

# FIRST kay is copied using SCP
scp C:\Users\wcd\.ssh\id_github_wcd.pub wcd@wcdPC:C:\ProgramData\ssh\administrators_authorized_keys

# SECOND and subsequent keys require some extra steps.
# Use SCP to copy the key with a temp name. 
scp C:\Users\wcd\.ssh\id_github_swatchpie.pub wcd@wcdPC:C:\ProgramData\ssh\temp_key.pub

# On the server, append the key to administrators_authorized_keys:
type C:\ProgramData\ssh\temp_key.pub >> C:\ProgramData\ssh\administrators_authorized_keys

# Remove the temp file.
del C:\ProgramData\ssh\temp_key.pub

# Exit the Server
exit

# Confirm contents
Get-Content C:\ProgramData\ssh\administrators_authorized_keys

# Check permissions
Get-Acl C:\ProgramData\ssh\administrators_authorized_keys | Format-List

# These should be 'FullControl'
Access : NT AUTHORITY\SYSTEM Allow  FullControl
         BUILTIN\Administrators Allow  FullControl

# Set permissions
ssh --% wcd@wcdPC icacls.exe "C:\ProgramData\ssh\administrators_authorized_keys" /inheritance:r /grant "Administrators:F" /grant "SYSTEM:F"         
```

## Test Key Pairs

Test keys by using them to log on to your computer's own OpenSSH Server:

```powershell
ssh -i C:\Users\wcd\.ssh\id_github_wcd wcd@wcdPC
exit

ssh -i C:\Users\wcd\.ssh\id_github_swatchpie wcd@wcdPC
exit
```

## Secure the Keys

At this point: 

- The private key has been added to the SSH Agent.
- The public key has been deployed to the SSH Server. 
- The keys are physically located at `C:\Users\wcd\.ssh`.

Use the following to secure this directory so that you, the user, are the only one who can access them.

```powershell
# Set permissions
icacls.exe "C:\Users\wcd\.ssh" /inheritance:r /grant:r wcd:F
icacls.exe "C:\Users\wcd\.ssh\id_*" /inheritance:r /grant:r wcd:F

# Confirm permissions
icacls.exe "C:\Users\wcd\.ssh"

NT AUTHORITY\SYSTEM:(OI)(CI)(F)
BUILTIN\Administrators:(OI)(CI)(F)
wcdPC\wcd:(OI)(CI)(F)
```

## Access Public Keys

At some point you may need to copy your public key. 

```powershell
# Copy public key to clipboard
Get-Content C:\Users\wcd\.ssh\id_github_wcd.pub | clip

# Show key in PS
Get-Content C:\Users\wcd\.ssh\id_github_wcd.pub
```

## Remove OpenSSH

I encountered an issue where, after a few months of non-use, I was unable to log in to my computer's SSH Server. After hours of troubleshooting, I've decided to remove it from my system and start again. 

```powershell
# 1. Uninstall OpenSSH Server and Client

# Uninstall OpenSSH Client
Remove-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

# Uninstall OpenSSH Server
Remove-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0

# Verify packages are removed.
# OpenSSH.Client and SSH.Server should read 'Not Present'
Get-WindowsCapability -Online | Where-Object {$_.Name -like 'OpenSSH*'}


# 2. Stop and Remove OpenSSH Services

# Check if any OpenSSH services are running
Get-Service -Name *ssh*

# Stop running services like this
Stop-Service sshd
Stop-Service ssh-agent

# Delete the services from your system
sc.exe delete sshd
sc.exe delete ssh-agent

# This message is OK
The specified service does not exist as an installed service.


# 3. Remove OpenSSH Configuration Files

# Manually navigate to and delete this directory:
# C:\ProgramData\ssh


# 4. Clean Up Registry entries

# Win + R, regedit
# Navigate to these keys and delete any OpenSSH or ssh related entries:
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services
HKEY_LOCAL_MACHINE\SOFTWARE\OpenSSH
HKEY_CURRENT_USER\SOFTWARE\OpenSSH

# 5. Restart and confirm removal

# No components
Get-WindowsCapability -Online | Where-Object {$_.Name -like 'OpenSSH*'}

# No services
Get-Service -Name *ssh*

```

## References

* Microsoft: [Get started with OpenSSH](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse)
* Microsoft: [OpenSSH key management](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement)


