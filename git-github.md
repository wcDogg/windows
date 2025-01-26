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

## Create Git Profiles

Profiles in Git for Windows are used by GitHub to associate credentials with repositories. In Git there is a global profile and repository profiles. 

### Single GitHub Account

If you have a single GitHub account, you can create a global Git profile: 

```powershell
# List the global user
git config --global --list

# Add or update the global user
git config --global user.name "wcDogg"
git config --global user.email "wcDogg@users.noreply.github.com"

# Remove the global user
git config --global --unset user.name "wcDogg"
git config --global --unset user.email "wcDogg@users.noreply.github.com"
```

### Multiple GitHub Accounts

If you have multiple GitHub accounts, you can either: 

- Use a global profile in combination with repository profiles as-needed.
- Use all repository profiles. 

**I strongly recommend using all repository-level profiles!!!** This avoids a messy scenario where you are working on a repo for your non-global account, forget to set the repo-level profile, and accidentally make a commit using the wrong profile. In theory, GitHub should reject the push because the emails won't match, but weird things can happen. I feel it's best not to risk it. 

The easiest way to use a repository profile is to open the repo in VS Code and use the Terminal from there.

```powershell
git config --list

git config user.name "wcDogg"
git config user.email "wcDogg@users.noreply.github.com"

git config user.name "swatchpie"
git config user.email "swatchpie@users.noreply.github.com"
```

## Setting Safe Directories

My local repositories are on an external `F:/` drive. When working with a `git` command, I got this error:

```powershell
fatal: detected dubious ownership in repository at 'F:/wcDogg/windows'
'F:/wcDogg/windows' is on a file system that does not record ownership...
```

To fix or prevent this, you must add the directory explicitly to Git's global configuration. (You cannot use just `F:/` here.)

```powershell
# Mark as safe
git config --global --add safe.directory F:/wcDogg/windows

# Restart the terminal and confirm:
git config --global --get-all safe.directory
``` 

## Update New and Existing Local Repositories

As mentioned above, if you are working with multiple GitHub accounts, **I strongly recommend using all repo-level Git profiles**. When you do, there are a few things that should become routine each time you clone a repository. These steps should also be performed for existing local repositories. 

For me, the first thing is to mark the directory as safe in my global Git configuration: 

```powershell
# Mark as safe
git config --global --add safe.directory F:/wcDogg/windows

# Restart the terminal and confirm:
git config --global --get-all safe.directory
``` 

Second is to set the correct Git profile. 

```powershell
git config user.name "wcDogg"
git config user.email "wcDogg@users.noreply.github.com"

git config user.name "swatchpie"
git config user.email "SwatchPie@users.noreply.github.com"
```

Because this needs to be routine, I wrote a PowerShell script to ease the process:

```powershell
# Open profile 
Code $Home\Documents\PowerShell\Profile.ps1

# Set Git profile / GitHub user
function set-gh {
  Write-Host "-------------------------------------------------------------"
  Write-Host "GITHUB REPO SETTINGS"
  Write-Host ""
  
  # Step 1: Confirm the current directory is a GitHub repository
  if (-not (Test-Path ".git")) {
    Write-Host "ERROR: The current directory is not a Git repository." -ForegroundColor Red
    return
  } 
  Write-Host "SUCCESS: Repository found." -ForegroundColor Green

  # Step 2: Add the current directory as a safe directory
  $currentDir = Get-Location
  git config --global --add safe.directory $currentDir
  Write-Host "SUCCESS: Git safe directory added: $currentDir" -ForegroundColor Green

  # Step 3: Restart the terminal session
  Write-Host "WAIT: Refreshing the terminal to apply changes..." -ForegroundColor Yellow
  $Host.UI.RawUI.FlushInputBuffer()
  [System.Console]::Clear()

  Write-Host "-------------------------------------------------------------"
  Write-Host "GITHUB REPO SETTINGS"
  Write-Host ""
  Write-Host "SUCCESS: Repository found." -ForegroundColor Green
  Write-Host "SUCCESS: Git safe directory added: $currentDir" -ForegroundColor Green

  # Step 4: Confirm the current directory is a safe directory
  $safeDirs = git config --global --get-all safe.directory
  if ($safeDirs -notcontains $currentDir) {
    Write-Host "ERROR: Git safe directory not found: $currentDir" -ForegroundColor Red
    return
  }
  Write-Host "SUCCESS: Git safe directory found: $currentDir" -ForegroundColor Green

  # Step 5: Prompt the user to choose a GitHub user
  $choice = Read-Host "PROMPT: Which GitHub user do you want to use? [1] wcDogg [2] SwatchPie" 
  switch ($choice) {
    '1' {
      git config user.name "wcDogg"
      git config user.email "wcDogg@users.noreply.github.com"
      Write-Host "SUCCESS: Set Git profile to wcDogg." -ForegroundColor Green
    }
    '2' {
      git config user.name "swatchpie"
      git config user.email "SwatchPie@users.noreply.github.com"
      Write-Host "SUCCESS: Set Git profile to SwatchPie." -ForegroundColor Green
    }
    default {
      Write-Host "WARNING: Invalid choice. No changes were made." -ForegroundColor Yellow
      return
    }
  }

  # Step 6: Confirm the profile has been set
  $gitName = git config user.name
  $gitEmail = git config user.email
  Write-Host "GitHub user:  $gitName " -ForegroundColor Cyan
  Write-Host "GitHub email: $gitEmail" -ForegroundColor Cyan
  Write-Host "-------------------------------------------------------------"
}
```



## Update Local Repository Remotes

If you have cloned GitHub repositories already on your computer, you will/may need to: 

- Add the repo to Git's list of safe directories.
- Update Git profile associated with the repository. 
- Update the repository remote to use the correct  `.ssh/config` Host. 

Open the repository in VS Code and use the Terminal from there. 

```powershell
# Recall my hosts are:
github-wcd
github-swatchpie

# Check the current remote
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
