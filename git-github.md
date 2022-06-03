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
* VS Code Bash terminal
* Git Bash terminal 

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
? TODO the propmt here auto-detects keys stored at `C:\Users\user\.ssh\` - select it.
? How would you like to authenticate GitHub CLI? Paste an authentication token
? Paste your authentication token: ****************************************
- gh config set -h github.com git_protocol ssh
✓ Configured git protocol
✓ Logged in as wcDogg
```

## Basic Git Commands

```
# Clone existing repo
git clone https://github.com/SqueezeSoftware/JSONpie.git

# Stage + commit changes
git add . && git commit -m "commit message"
```

## New Repo from Local Directory

See: [Adding an existing project to GitHub using the command line](https://docs.github.com/en/get-started/importing-your-projects-to-github/importing-source-code-to-github/adding-an-existing-project-to-github-using-the-command-line)

```
# Copy a local folder (template)
Copy-Item -Path "D:\github\.project" -Destination "D:\github\pngsqu" -recurse -Force

# cd to the new directory and view contents
cd D:\github\test
Get-ChildItem

# Create the repo
git init -b main
git add . && git commit -m "initial commit"
gh repo create

# Follow these prompts
Initialized empty Git repository in D:/GitHub/test/.git/

? What would you like to do? Push an existing local repository to GitHub
? Path to local repository (.)

? Path to local repository .
? Repository name (test)

? Repository name test
? Description A short description

? Description A short description
? Visibility Public
✓ Created repository wcDogg/test on GitHub
? Add a remote? Yes
? What should the new remote be called? (origin)

? What should the new remote be called? origin
✓ Added remote git@github.com:wcDogg/test.git
? Would you like to push commits from the current branch to "origin"? Yes
✓ Pushed commits to git@github.com:wcDogg/test.git
```

