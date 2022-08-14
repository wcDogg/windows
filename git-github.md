## Git for Windows + GitHub

How to install and configure Git for Windows to use OpenSSH on Windows 11 Pro.

## OpenSSH

These instructions are specific to OpenSSH and assume you have already created your key pair. 

See: [Windows 11 Pro OpenSSH](windows-11-pro-openSSH.md)

Your public key should be located in `C:\Users\user\.ssh\`. A key in this location will be automatically detected during GitHub authentication.

## GitHub Emails

[Prevent your GitHub email address from being used in commits](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-email-preferences/setting-your-commit-email-address) by: 

1. User menu > Settings > Left panel > Emails
2. Check **Keep my emails private**.
3. Check **Block command line pushes that expose my email**.

This sets your commit address to `username@users.noreply.github.com`

## GitHub SSH Key

This step is NOT necessary - just noting where keys wind up. 

1. User menu > Settings 
1. Left panel > Access: SSH & GPG Keys > New SSH key
1. Paste your public key and save. 

## GitHub Personal Access Token

1. User menu > Settings
2. Left panel > Developer Settings > Personal Access Tokens 
3. In the top-right corner, click Add New.
4. Name the token and set an expiration date. 
5. Check the boxes for these scopes: `repo`, `workflow`, `read:org`. 
6. Click Generate Token. Copy it to a (`.env`) or password manager.

## Install Git

Here is a slideshow of the settings I used: 

![](./images/git-install/git-install.mp4)

## Bash

Instructions can be performed in any terminal that understands Bash. This does NOT include Windows PowerShell. This does include:

* [PowerShell](https://github.com/PowerShell/PowerShell)
* Windows Command Prompt 
* Windows Terminal with Bash profile
* Git Bash terminal 
* VS Code integrated Bash terminal

## Configure Git Profile

Use your GitHub username and the noreply email you configured in a previous step. 

```
git --version
git config --global user.name "wcDogg"
git config --global user.email "wcDogg@users.noreply.github.com"
git config --global --list
```

## Install GitHub CLI + Authenticate

[GitHub CLI](https://cli.github.com/)

For this step you will need your GitHub username and personal access token 

```
gh auth login

? What account do you want to log into? GitHub.com
? What is your preferred protocol for Git operations? SSH
? Generate a new SSH key to add to your GitHub account? No
? TODO the prompt here auto-detects keys stored at `C:\Users\user\.ssh\` - select it.
? How would you like to authenticate GitHub CLI? Paste an authentication token
? Paste your authentication token: ****************************************
- gh config set -h github.com git_protocol ssh
✓ Configured git protocol
✓ Logged in as wcDogg
```

## New Repo from Local Directory

See: [Adding an existing project to GitHub using the command line](https://docs.github.com/en/get-started/importing-your-projects-to-github/importing-source-code-to-github/adding-an-existing-project-to-github-using-the-command-line)

```powershell
# Create directory 
New-Item -Path 'D:\GitHub\my-project' -ItemType Directory
cd D:\GitHub\my-project



# Use a template or: 
# Add README.md
# Add .gitignore
# Add license

# View contents
Get-ChildItem

# Create the repo
git init -b main
git add . && git commit -m "Initial commit"
git status
gh repo create

# Follow the prompts - answers are as follows: 
? What would you like to do? Push an existing local repository to GitHub
? Path to local repository .
? Repository name test
? Description A short description
? Visibility Public
? Add a remote? Yes
? What should the new remote be called? origin
? Would you like to push commits from the current branch to "origin"? Yes
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

## Connect an Existing Repo to Existing Local Directory

```powershell
# Connect - 'origin' is the 'name' of this URL
remote add origin <repo URL>

# View all of the URLs associated with a repo
git remote -v
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

