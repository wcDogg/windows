## Git for Windows + GitHub

How to install and configure Git for Windows to use OpenSSH on Windows 11 Pro.

## Bash

All of the bash commands used here can be run in [PowerShell](./powershell.md) as an admin.

Instructions can be performed in any terminal that understands Bash. This does NOT include Windows PowerShell. This does include:

* [PowerShell](https://github.com/PowerShell/PowerShell)
* Windows Command Prompt 
* Windows Terminal with Bash profile
* Git Bash terminal (installed with Git)
* VS Code integrated Bash terminal

## GitHub Emails

[Prevent your GitHub email address from being used in commits](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-email-preferences/setting-your-commit-email-address) by: 

1. User menu > Settings > Left panel > Emails
2. Check **Keep my emails private**. 
3. Check **Block command line pushes that expose my email**.

Your Github commit address appears in the fine print under **Keep my emails private**. 

* It looks something like `123456789+username@users.noreply.github.com`
* In subsequent steps you will use `username@users.noreply.github.com`

## Generate SSH Key Pairs

To use SSH, you will need a key pair for each GitHub account.

- Follow the instructions in [Windows OpenSSH](openSSH.md) to create key pairs.
- Be sure to add keys to the SSH Server and Client.
- GitHub uses **Ed25519 encryption** for key pairs. 
- Your keys should be located in `C:\Users\user\.ssh\`. 
- Keys in this location will be automatically detected during GitHub authentication.

These are used in the examples:

```
id_github_wcd
id_github_swatchpie
```

## Generate GitHub Personal Access Tokens

To use SSH, you will need a GitHub PAT for each account.

1. User menu > Settings
2. Left panel > Developer Settings > Personal Access Tokens 
3. In the top-right corner, click Add New.
4. Name the token and set an expiration date. 
5. Check the boxes for these scopes: `repo`, `workflow`, `read:org`, `admin:public_key`
6. Click Generate Token. Copy it to a `.env` or password manager.

## Add Public Keys to GitHub

To copy a public key to your clipboard: 

```powershell
Get-Content C:\Users\wcd\.ssh\id_github_wcd.pub | clip
Get-Content C:\Users\wcd\.ssh\id_github_swatchpie.pub | clip
```

Public keys are added here: 

1. User menu > Settings 
1. Left panel > Access: SSH & GPG Keys > New SSH key
1. Set an informative 'Title'.
1. Set the 'Key Type' to 'Authentication Key'.
1. Paste your public key and save.



## Update OpenSSH Configuration

I have 2 GitHub accounts that I will be using SSH with. To make scenarios like this easier, update the OpenSSH configuration. 

```powershell
# Open the config file
code "$HOME\.ssh\config"

# Add this and save
Host github-wcd
  HostName github.com
  User git
  IdentityFile C:\Users\wcd\.ssh\id_github_wcd

Host github-swatchpie
  HostName github.com
  User git
  IdentityFile C:\Users\wcd\.ssh\id_github_swatchpie

# Test
ssh -T github-wcd
ssh -T github-swatchpie

# You will see something like:
The authenticity of host 'github.com (140.82.113.3)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added 'github.com' (ED25519) to the list of known hosts.
git@github.com: Permission denied (publickey).

```

## Install Git for Windows

[Git for Windows](https://gitforwindows.org/)

Be sure to look for and enable the `Add to Path` option.

```powershell
git --version
```

## Install GitHub CLI

Install [GitHub CLI](https://cli.github.com/)

Be sure to look for and enable the `Add to Path` option.

```powershell
gh --version
```

## Test GitHub Authentication

This step tests that key pairs have been properly configured in OpenSSH and on GitHub. 

```powershell
# Test
ssh -T github-wcd
ssh -T github-swatchpie

# Result should look like: 
Hi wcDogg! You've successfully authenticated, but GitHub does not provide shell access.
```

## Update Local Repository `remote`

If you have cloned GitHub repositories on your computer, you may need to update the `remote` to use the correct `Host` located in your `.ssh/config` file. 

Open the repository in VS Code and use the Terminal from there. 

```powershell
# Recall my hosts are:
github-wcd
github-swatchpie

# Check the current remote
git remote -v

# You may see this:
fatal: detected dubious ownership in repository at 'F:/wcDogg/windows'
'F:/wcDogg/windows' is on a file system that does not record ownership...

# To fix, you must use explicit directories.
# Using just `F:/` will not mark the drive as safe.
git config --global --add safe.directory F:/wcDogg/windows

# Restart the terminal and confirm safe directory:
git config --global --get-all safe.directory

# Then try again:
git remote -v

# You will see something like:
origin  https://github.com/wcDogg/windows.git (fetch)
origin  https://github.com/wcDogg/windows.git (push)

# Update the remote and verify.
git remote set-url origin git@github-wcd:wcDogg/windows.git
git remote -v

# You should see something like the following.
# Note how the remote now uses the `github-wcd` host we configured.
origin  git@github-wcd:wcDogg/windows.git (fetch)
origin  git@github-wcd:wcDogg/windows.git (push)
```

Once you have completed the steps above, VS Code and GitHub should be communicating. 

1. Make a test change to the repo. 
2. Note that VS Code indicates that changes are waiting for commit. 
3. Switch to the 'Source Control' panel 
4. Use the VS Code UI to push the change. 


## Update Git for Windows

Once Git for Windows is installed, you can use **any bash terminal as an admin** to update it.

```bash
git --version
git update-git-for-windows
git --version
```

## Update GitHub CLI

Once the GitHub CLI is installed, you can use **any bash terminal as an admin** to update it. 

```bash
gh --version
gh upgrade
gh --version
```

