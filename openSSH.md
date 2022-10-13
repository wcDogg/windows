# Windows OpenSSH

Logging into remote services with a username & password is vulnerable to eavesdropping, connection hijacking, brute force, and other attacks.

OpenSSH uses the SSH protocol for remote login. It uses key pairs for authentication and to encrypt all traffic between client and server. Built-in SSH has always been a Linux thing. Windows 10 and higher also supports OpenSSH. Git and other popular services support & recommend SSH. 


## Scenario

* I am the only user on my machine and I'm an admin user. 
* It doesn't matter that I have a [local account](local-account.md) vs a Microsoft account. 
* Commands can be run in Windows PowerShell or PowerShell as an administrator.


## Check Services

Start > Services > 

* OpenSSH Authentication Agent: Startup type = Auto / Manual 
* OpenSSH SSH Server: Startup type = Auto

## Install OpenSSH Client & Server

In PowerShell as admin: 

```powershell
# Check
Get-WindowsCapability -Online | Where-Object Name -like 'OpenSSH*'

# Install as needed
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

## Start the SSH service (server)

Continue in PowerShell as admin: 

```powershell
Start-Service sshd
Set-Service -Name sshd -StartupType 'Automatic'
```

## Confirm Firewall Rule

* Installing OpenSSH Server should create & enable a firewall rule named 'OpenSSH-Server-In-TCP'.
* This allows inbound SSH traffic on port 22. 
* If this rule is not enabled and this port is not open, connections will be refused or reset. 
* I run a 3rd-party firewall that is recognized by the baked-in Windows Defender Firewall. For me, this rule was set and works as expected. 

Continue in PS as admin: 

```powershell
# Check firewall and add rule if needed
if (Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -ErrorAction SilentlyContinue | Select-Object Name, Enabled) {
    Write-Output "Firewall rule 'OpenSSH-Server-In-TCP' already exists. No changes made."
} else {
    Write-Output "Firewall Rule 'OpenSSH-Server-In-TCP' does not exist, creating it..."
    New-NetFirewallRule -Name 'OpenSSH-Server-In-TCP' -DisplayName 'OpenSSH Server (sshd)' -Enabled True -Direction Inbound -Protocol TCP -Action Allow -LocalPort 22
    Write-Output "Firewall rule created."
}
```

## Confirm user and machine names

In the following steps, use `username@servername`. For a single user on a single PC, this is your Windows account username and your PC's name - wcd@wcdPC. You can use these to confirm: 

```powershell
$env:USERNAME
[System.Net.Dns]::GetHostName()
```

## Locate Server IP

There are 2 ways to connect to the OpenSSH server:

* username@servername
* username@server-ip-address

```powershell
ipconfig | select-string  ('(\s)+IPv4.+\s(?<IP>(\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}))(\s)*') -AllMatches | %{ $_.Matches } | % { $_.Groups["IP"]} | %{ $_.Value }
```

I ran through these steps twice.

* The first time, the command above showed 2 IPs. One was my LAN address (192.xxx.x.xxx), so I correctly guessed that the other was my server (10.x.x.x).
* The second time, the command above only returned my LAN IP, so I used my PC name instead.


## Connect to OpenSSH Server

Continue in PowerShell as admin. This step adds the SSH server IP to the list of known hosts:

```powershell
ssh wcd@wcdPC

# Authenticity...continue? yes
# Permanently added...
# Conntection reset...
```

Continue in PowerShell as admin. This step logs you in to the SSH server. This step worked if the screen clears and the prompt changes. 

```powershell
ssh wcd@wcdPC

# Windows account password
# Screen clears, prompt changes

wcd@WCDPC C:\Users\wcd>
```

## Generate a Key Pair

Continue in same shell connected to the SSH server. Your prompt should look like `wcd@wcdPC C:\Users\wcd>`.

Before starting: 

1. Identify the encryption algorithm needed - for GitHub it's Ed25519.
2. Decide a file name - `id_wcd_ed25519`.
3. Decide a passphrase to encrypt private key.
4. Prepare a password manager entry. Use this to store the key's passphrase and SHA256 fingerprint.

To generate a key pair: 

1. Use the command below. Follow prompts to set file and passphrase.
2. Create file in the default `C:\Users\wcd\.ssh/` folder. Note the `/`.
3. The result is a public key, a private key, and a SHA256 fingerprint + randomart.  
4. Add passphrase and SHA256 fingerprint to a password manager. 

```powershell
ssh-keygen -t ed25519

# C:\Users\wcd\.ssh/id_gh_ed25519
# Create passphrase for private key file
```

## Add private key to Windows security context

Leave the connected shell running. 

**Open a new PowerShell as an admin**. Your prompt should look like `PS C:\Windows\System32>`. For this step you will need the private key's file path and passphrase. 

This step stores the private key in a Windows security context associated with your Windows login. This context is perhaps the most protected and least vulnerable 'area' of a computer. Whenever a private key is needed for authentication, ssh-agent automatically retrieves the private key and passes it to the SSH client.

```powershell
Get-Service ssh-agent | Set-Service -StartupType Manual
Start-Service ssh-agent
Get-Service ssh-agent
ssh-add C:\Users\wcd\.ssh\id_wcd_ed25519

# Private key passphrase
# Identity added: C:\Users\wcd\.ssh\id_wcd_ed25519 (wcd@wcdPC)
```
Note: Microsoft's instructions use `~\.ssh\` instead of the full path. This didn't work for me - the full path did. Shallow research says this is a mismatch between how Windows and PowerShell use the tilde `~`. 


## Add Public Key to OpenSSH Server

Stay in the new, un-connected shell - your prompt should look like `PS C:\Windows\System32>`.

To use the key pair, the contents of the public key need to be placed into a text file on the OpenSSH server. 

* For admin users, the contents of the public key must be securely copied to: `C:\ProgramData\ssh\administrators_authorized_keys`. 
* See the Microsoft resources for standard users and multiple admin users. 
* To copy we use `scp` - a secure file-transfer utility provided by the OpenSSH client. 
* The ACL on this file must restrict access to administrators and System.
* You will need your Windows account password for this step.

```powershell
# Check for / create .ssh directory for current user.
ssh wcd@wcdPC mkdir C:\ProgramData\ssh\

# Windows password
# Confirmation message

# OPTION 1: First key
# Use `scp` to copy puhlic key to SSH server.
scp C:\Users\wcd\.ssh\id_wcd_ed25519.pub wcd@wcdPC:C:\ProgramData\ssh\administrators_authorized_keys

# OPTION 2: A second or subsequent key
# Append new public key to authorized_keys
cat C:\Users\wcd\.ssh\id_wcd_rsa4096.pub >> C:\ProgramData\ssh\administrators_authorized_keys

# Account passowrd
# Displays public key file name as confirmation

# ACL the authorized_keys file on the server:
ssh --% wcd@wcdPC icacls.exe "C:\ProgramData\ssh\administrators_authorized_keys" /inheritance:r /grant "Administrators:F" /grant "SYSTEM:F"

# Account password
# Processed files confirmation
```

## Backup Keys 

At this point the private key has been added to the Windows security context and the public key has been deployed to the OpenSSH server. 

Right now, there are local copies at `C:\Users\wcd\.ssh`. Backup both keys to a secure location. Leave the public key in this folder for easy access. Delete the private key in this folder. 


## Access Public Keys

At some point you may need to copy your public key. 

```powershell
# Copy public key to clipboard
Get-Content C:\Users\wcd\.ssh\id_wcd_ed25519.pub | clip

# Show key in PS
Get-Content C:\Users\wcd\.ssh\id_wcd_ed25519.pub
```

## Start SSH Agent when PowerShell Loads

See [Powershell](powershell.md) for how to create a profile and start OpenSSH automatically.


## References

* Microsoft: [Get started with OpenSSH](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse)
* Microsoft: [OpenSSH key management](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement)


