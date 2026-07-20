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



## Add OpenSSH Hosts

I have 2 GitHub accounts that I will be using SSH with. To make scenarios like this easier, update the OpenSSH configuration. 

```powershell
# Open the config file
code "$HOME\.ssh\config"

# Add this and save - note `/`
Host github-wcd
  HostName github.com
  User git
  IdentityFile C:/Users/wcd/.ssh/id_github_wcd

Host github-swatchpie
  HostName github.com
  User git
  IdentityFile C:/Users/wcd/.ssh/id_github_swatchpie

# Test
ssh -T github-wcd
ssh -T github-swatchpie

# In subsequent steps you will see:
git@github-wcd
git@github-swatchpie
```

## Install Git for Windows

1. Download [Git for Windows](https://gitforwindows.org/) and run the installer.
2. Confirm with `git --version`.

See the [settings I used](./images/git-install/)

Many tutorials say to check for these, but I did not see either during installation:

- Look for and ensure `Add to PATH` is enabled. 
- Look for and ensure `allow Unix-style /mnt drive mappings` is enabled.

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
# VS Code repo 

# Show the current config
# Use `q` to exit.
git config --list
q

# Add profiles
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
# List safe directories
git config --global --list

# Mark as safe
git config --global --add safe.directory "F:/wcDogg/windows"
git config --global --add safe.directory "F:\\wcDogg\\windows"
git config --global --add safe.directory "F:/Script-Hero"

# Remove all safe directories
git config --global --unset-all safe.directory

# Remove a specific safe directory
git config --global --unset safe.directory F:/wcDogg/windows
``` 

## Update Existing Local Repos

As mentioned above, if you are working with multiple GitHub accounts, **I strongly recommend using all repo-level Git profiles**. When you do, there are a few things that should become routine. These steps should also be performed for existing local repositories. 

For me, the first thing is to mark the directory as safe in my global Git configuration and set the correct Git profile.

```powershell
# Mark directory as safe
git config --global --add safe.directory "F:/wcDogg/windows"
git config --global --add safe.directory "F:\\wcDogg\\windows"

# Set the Git profile
git config user.name "wcDogg"
git config user.email "wcDogg@users.noreply.github.com"

git config user.name "swatchpie"
git config user.email "SwatchPie@users.noreply.github.com"
```

And the next thing is to update the remote.

```powershell
# Recall my hosts are:
github-wcd
github-swatchpie

# Check the current remote
git remote -v

# See something like:
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


## New Repo from Local Directory

Assumes local directory already contains a `README.md`, `.gitignore`, and `LICENSE`. 

```powershell
# Initialize the repo and confirm
git init -b main

# Mark directory as safe and confirm
git config --global --add safe.directory "F:/Script-Hero"
git config --global --list

# Set the Git profile and confirm
git config user.name "wcDogg"
git config user.email "wcDogg@users.noreply.github.com"

git config user.name "swatchpie"
git config user.email "SwatchPie@users.noreply.github.com"

git config --get user.name
git config --get user.email

# Confirm Git initialized correctly
git rev-parse --is-inside-work-tree

# Test GH authentication
gh auth status

>> github.com
>>   ✓ Logged in to github.com as SwatchPie (keyring)
>>   ✓ Git operations for github.com configured to use ssh protocol.
>>   ✓ Token: ghp_************************************
>>   ✓ Token scopes: admin:public_key, read:org, repo, workflow

# Create a public repo
gh repo create SwatchPie/script-hero-for-illustrator --public --source . --remote ssh

>> ✓ Created repository SwatchPie/script-hero-for-illustrator on GitHub
>> ✓ Added remote git@github.com:SwatchPie/script-hero-for-illustrator.git

# Create a private repo
gh repo create SwatchPie/script-hero-for-illustrator --private --source . --remote ssh

# Check the remote - note it is not using the SSH hosts
git remote -v

>> ssh     git@github.com:SwatchPie/script-hero-for-illustrator.git (fetch)
>> ssh     git@github.com:SwatchPie/script-hero-for-illustrator.git (push)

# Update remote to use SSH host
git remote set-url ssh git@github-swatchpie:SwatchPie/script-hero-for-illustrator.git

# Check the remote - it should now use the SSH host
git remote -v

>> ssh   git@github-swatchpie:SwatchPie/script-hero-for-illustrator.git (fetch)
>> ssh   git@github-swatchpie:SwatchPie/script-hero-for-illustrator.git (push)

# Stage and commit initial content
git add . && git commit -m "Initial commit"

>> 3 files changed, 32 insertions(+)
>> create mode 100644 .gitignore
>> create mode 100644 LICENSE
>> create mode 100644 README.md

# Push to GitHub
git push ssh main

>> Enumerating objects: 5, done.
>> Counting objects: 100% (5/5), done.
>> Delta compression using up to 24 threads
>> Compressing objects: 100% (4/4), done.
>> Writing objects: 100% (5/5), 881 bytes | 881.00 KiB/s, done.
>> Total 5 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
>> To github-swatchpie:SwatchPie/ghithub-test.git
>>  * [new branch]      main -> main

# Verify on GitHub.
# Make a test change.
# Use VS Code UI to commit the change. 
```

## Basic Git Commands

* https://www.youtube.com/watch?v=apGV9Kg7ics
* https://www.youtube.com/watch?v=SD7YNLv5Evc

```powershell
# Clone existing repo
git clone https://github.com/SqueezeSoftware/JSONpie.git

# See the status of changes
git status

# Stage changes
git add .
git add file.txt
git add dir-name

# Remove file from stage
git restore --staged README.md

# Commit 
git commit -m "Commit message"

# Stage + commit all changes
git add . && git commit -m "Commit message"

# Push changes to current branch
git push

# Push changes to a different branch
git push origin branchname

# View commit history
git log

# You can also do this - press Q to exit
git log --all --graph

# See the contents of a file
cat README.md
```

## Revert to an Earlier Commit not yet Pushed

```powershell
# Get the commit's ID
git log

# Revert
git reset f73d30fa32fd393813714c533357447f435d3f06

# View un-staged changes
git status
```

## Stashing Changes 

Stash only works once the project has an initial commit.

```powershell
# Stage what will be stashed
git add .

# Review
git status

# Stash 
git stash

# Bring stashed back
git stash pop

# Discard stashed - permanent + irreversible
git stash clear
```

## Branching

```powershell
# List local branches
git branch

# List remote branches
git branch -r

# Create local + push to GitHub
git branch dbot
git push -u origin dbot

# Switch branches
git checkout dbot

# Merge branch to main
git checkout main
git merge dbot -m "Dependency version updates"
git push
```

## PowerShell Function to Update Repositories

Because this needs to be routine, I wrote a PowerShell `set-sh` script to mark directories as safe and set the Git profile. Note it does not update local remotes. 

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
