# Repomix

[Repomix](https://repomix.com/) is a command line tool that packs an entire project into a single file designed for AI to synthesize.


## 1. Ignore File

At project root, create a `.repomixignore` file with the following:

```
node_modules/
dist/
.git/
package-lock.json

```

## 2. Run Repomix

```PowerShell
# Local project
npx repomix --style markdown

# GitHub repo
npx repomix --remote 'https://github.com/AllwineDesigns/gridmaker/tree/master' --style markdown

```
