# Node JS


## 1. NVM for Windows

[NVM](nvmnode.com/guide/download.html)

NVM lets us manage multiple versions of Node.js and use `npx`.

```powershell
# Install NVM
winget install CoreyButler.NVMforWindows

# Restart PowerShell and validate
nvm version

```

## 2. Node.js

```PowerShell
# 1. Install the Long Term Support (LTS) version.
nvm install lts

# 2. Tell Windows to use it (Switch to Admin if it errors).
nvm use lts

# 3. Validate.
node -v
npm -v

```
