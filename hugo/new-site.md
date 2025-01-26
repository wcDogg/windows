# Create a New HUGO Site

How to create a new HUGO site and set up the basics.

## Create a New Site

1. Create a new directory `hugo-test`.
2. Open directory in VS Code. 
3. Open your VS Code Terminal (PowerShell).

```powershell
# Command
hugo new site ./

# Response
Congratulations! Your new Hugo site was created in F:\hugo-test.

Just a few more steps...

1. Change the current directory to F:\hugo-test.
2. Create or install a theme:
   - Create a new theme with the command "hugo new theme <THEMENAME>"
   - Or, install a theme from https://themes.gohugo.io/
3. Edit hugo.toml, setting the "theme" property to the theme name.
4. Create new content with the command "hugo new content <SECTIONNAME>\<FILENAME>.<FORMAT>".
5. Start the embedded web server with the command "hugo server --buildDrafts".

See documentation at https://gohugo.io/.
```

## Set Front Matter to use YAML

Hugo uses TOML by default. To format front matter using YAML, edit `hugo.toml`.

```toml
[frontmatter]
format = "yaml"
date = "date"
expiryDate = "expiryDate"
publishDate = "publishDate"
```

## Theme Skeleton 

Our new site doesn't have content or a theme. And in the beginning, it's best to work without a theme. In this step, we create a temporary theme which generates files we will move to the site's skeleton. 

```powershell
hugo new theme temp
```

The result is a new `hugo-test/themes/temp/` directory with several sub-directories. 

1. Copy `hugo-test/themes/temp/layouts/` and its contents to `hugo-test/layouts/`.
1. Copy ` ` to ` `



## PowerShell Script

Hello. Please help me write a script I can run in PowerShell 7 to help me configure new Hugo sites. 

1. Call this `new-hugo-setup` or `newHugoSetup` or whatever is appropriate. 
1. Confirm a Hugo site does not already exist in this location.
    1. If one does already exist, inform user and stop. 
1. Confirm with yes/no that the user is currently in the directory where the Hugo site should be created. 
    1. If no, prompt user to change directories and start again, then stop.
1. Run `hugo new site ./`. Wait for process to complete.
1. Append the following to `hugo.toml`:

```toml
[frontmatter]
format = "yaml"
date = "date"
expiryDate = "expiryDate"
publishDate = "publishDate"
```
