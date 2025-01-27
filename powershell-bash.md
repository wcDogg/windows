# PowerShell + Git Bash

TL;DR: I'm using the Git Bash shell via PowerShell to run generic bash scripts. The correct format is:

```bash
bash /mnt/host/f/scripts/hello-world.sh
```

## Scenario

I use [PowerShell](./powershell.md) and [Git for Windows](./git-github.md#install-git-for-windows.). Git also installs a Git Bash shell which allows you to run `bash` commands in PowerShell. 

After months of not using bash scripts, I tried running one and kept getting `No such file`. So I'm writing this down for my future self. 

## Paths That Don't Work

```bash
# Unix path, expected to work
bash F:/scripts/hello-world.sh
>> /bin/bash: F:/scripts/hello-world.sh: No such file or directory

# Git Bash path, expected to work
bash /f/scripts/hello-world.sh
>> /bin/bash: /f/scripts/hello-world.sh: No such file or directory

# Git Bash absolute path, expected to work
bash //f/scripts/hello-world.sh
>> /bin/bash: //f/scripts/hello-world.sh: No such file or directory

# Windows path, not expected to work
bash F:\scripts\hello-world.sh
>> /bin/bash: F:scriptshello-world.sh: No such file or directory
```

## Check the Basics

```bash
# As admin, set PowerShell execution policy to allow running scripts.
# This cannot be configured via profiles. 
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned

# Check file exists, Windows
Test-Path F:\scripts\hello-world.sh
>> True

# Check Git installation.
# This opens the Git Bash shell in PowerShell.
bash
>> wcdPC:/mnt/host/c/Users/wcd#
```

## Find the Correct Path 

```bash
# Continue in bash shell...

# Check `F` drive is mounted - it is not. 
ls /mnt
>> host

# Navigate Git Bash to find the script's full path. 
ls -l /mnt/host
ls -l /mnt/host/f
ls -l /mnt/host/f/scripts

# Exit Git Bash shell
exit

# Try running script with the correct path.
bash /mnt/host/f/scripts/hello-world.sh
>> Hello, World!

```

## Simplify Running Scripts

STOP HERE. Since I have several scripts in `F:/scripts`, I wanted to make running these easier - say, the ability to to run `hello-world.sh` directly.

I tried for a few hours and was unsuccessful, so I stopped - it was becoming overkill for my needs. 

The following is my most recent, failed attempt. 

```bash
# Locate file
bash
ls -a ~/.bashrc
>> ls: /root/.bashrc: No such file or directory

# Create file
touch ~/.bashrc
ls -a ~/.bashrc
>> /root/.bashrc
exit

# This seems to open the .bashrc for $HOME.
# This is not the file we want. 
# code ~/.bashrc

# This opens the .bashrc for Git Bash.
# This is the file we want. 
code /root/.bashrc

# Add this and save
export PATH=$PATH:/mnt/host/f/scripts

# Reload
bash
. /root/.bashrc
exit

# Kill and restart PowerShell.

# Check loading. This should print 'Test'. 
echo "Test" >> /root/.bashrc
bash
>> Nothing printed

# Confirm drive is now on $PATH
echo $PATH
>> Nothing printed

# Try running script directly.
hello-world.sh
>> hello-world.sh: The term 'hello-world.sh' is not recognized...

# STOPPED here and deleted file.
bash
rm /root/.bashrc
ls -a /root/.bashrc
>> ls: /root/.bashrc: No such file or directory
exit

# And remove added line from $HOME .bashrc
code ~/.bashrc

```
