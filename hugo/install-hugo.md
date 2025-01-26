# Install HUGO on Windows


## Prerequisites

- [Node.js](../node-js.md)
- [Git](../git-github.md)
- [Chocolatey](../chocolatey.md)
- [VS Code](https://code.visualstudio.com/)


## Install HUGO and GoLang

- [Install Instructions](https://gohugo.io/installation/windows/)
- [Installing HUGO on Windows](https://www.youtube.com/watch?v=l7PHRA8t4Bw)

```powershell
# As an admin
choco install hugo-extended
choco install golang

# Kill and restart terminal as admin
# Verify installs
hugo version
go version
```
