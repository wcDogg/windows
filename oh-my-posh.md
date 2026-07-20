# Oh My Posh

- [Oh My Posh](https://ohmyposh.dev/)
- [Get Started > Windows](https://ohmyposh.dev/docs/installation/windows)


## 1. Install OMP

```powershell
# PowerShell 7 as Admin
winget install JanDeDobbeleer.OhMyPosh --source winget

# Validate
oh-my-posh -version

# Update
winget upgrade oh-my-posh

```

## 2. Install Nerd Font

Required if you want to use a theme with icons. I do not.

1. Preview and decided on a [Nerd Font](https://www.nerdfonts.com/font-downloads)
2. [OMP Instructions](https://ohmyposh.dev/docs/installation/fonts)


## 3. Get a Theme

I am using a theme without icons. 

1. Download a [theme](https://ohmyposh.dev/docs/themes#tokyo). 
2. Save to `C:\Users\userName\.theme-name.omp.json`.

```
C:\Users\ljbur\aliens.omp.json
```

## 4. Set Firewall Rule

Get the Oh My Posh `.exe` path and ensure your firewall allows this URI. 

```powershell
(Get-Command oh-my-posh).Source
# C:\Users\ljbur\AppData\Local\Microsoft\WindowsApps\oh-my-posh.exe

```

## 5. Add to PowerShell 7 Profile

Add this to your PowerShell `$PROFILE` and restart - see [Microsoft PowerShell](powershell.md).

```ps1
oh-my-posh init pwsh --config ~/aliens.omp.json | Invoke-Expression

```

