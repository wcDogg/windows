# Git Commands when using OpenSSH

Commands for working with GitHub Repos via PowerShell.


## New Repo from Local Directory

* [GitHub: Adding an existing project to GitHub using the command line](https://docs.github.com/en/get-started/importing-your-projects-to-github/importing-source-code-to-github/adding-an-existing-project-to-github-using-the-command-line)

```bash
# Create project directory 
New-Item -Path 'D:\GitHub\my-project' -ItemType Directory
# cd into project
cd D:\GitHub\my-project

# Use a template or: 
# Add README.md
# Add .gitignore
# Add LICENSE

# View contents
Get-ChildItem

# Create the repo
git init -b main
git add . && git commit -m "Initial commit"
git status
gh repo create

# Follow the prompts - answers are as follows: 
# ? What would you like to do? Push an existing local repository to GitHub
# ? Path to local repository .
# ? Repository name test
# ? Description A short description
# ? Visibility Public
# ? Add a remote? Yes
# ? What should the new remote be called? origin
# ? Would you like to push commits from the current branch to "origin"? Yes
```

## Connect an Existing Repo to Existing Local Directory

```powershell
# Connect - 'origin' is the 'name' of this URL
remote add origin <repo URL>

# View all of the URLs associated with a repo
git remote -v
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


