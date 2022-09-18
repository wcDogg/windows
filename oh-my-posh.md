# Oh My Posh

* [Oh My Posh](https://ohmyposh.dev/)
* [Get Started > Windows](https://ohmyposh.dev/docs/installation/windows)
* [Pick a theme](https://ohmyposh.dev/docs/themes) - default, 1_shell


## Get a Nerd Font

1. Download [Caskaydia Cove Nerd Font](https://www.nerdfonts.com/font-downloads).
2. Install `Caskaydia Cove Nerd Font Complete Mono Windows Compatible.ttf`.
3. In PowerShell, set `CaskaydiaCove NF` as the default font. (Right-clck title bar > Defaults > Font)


## Install Oh My Posh  

In PowerShell:

```powershell
winget install oh-my-posh
```

## Get a Theme

1. Download a [theme](https://ohmyposh.dev/docs/themes#tokyo). 
2. Save to `C:\Users\wcd\.theme-name.omp.json`.


## Set Firewall Rule

Get the Oh My Posh `.exe` path and ensure your firewall allows this URI. 

```powershell
(Get-Command oh-my-posh).Source
# C:\Users\wcd\AppData\Local\Programs\oh-my-posh\bin\oh-my-posh.exe
```

## Add to PowerShell Profile

Add this to the All Users profile and restart PS - see [PowerShell](powershell.md).

```ps1
oh-my-posh init pwsh --config ~/.theme-name.omp.json | Invoke-Expression
```

## Update

```powershell
winget upgrade oh-my-posh
```

