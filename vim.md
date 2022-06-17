# Vim Editior

* Windows prereq: [Chocolatey](chocolatey.md)
* [Vim Editor](https://www.vim.org/)
* Stanford University: [The Vim Editor](https://web.stanford.edu/class/cs107/resources/vim.html)
* freeCodeCamp: [vimrc Configuration Guide](https://www.freecodecamp.org/news/vimrc-configuration-guide-customize-your-vim-editor/)

## Install

```
# PS as admin
choco install vim -y
vim --version
```

## Check and Note Stuff

* Vim opens in Command mode. 
* Commands are entered in the bottom-left corner.

```
# C:\Users\wcd\AppData\Local\Temp
Get-ChildItem -Path Env:\TEMP

vim

# system vimrc file: $VIM\vimrc
# user vimrc file: $HOME\_vimrc
:version

# C:\Users\wcd
:echo expand('~'

# C:\Users\wcd
:echo $HOME

# C:\tools\vim
:echo $VIM

# C:\tools\vim\vim82
:echo $VIMRUNTIME
```

## Location of VIMRC

```
C:\tools\vim\vimrc
C:\Users\wcd\_vimrc
```

## Files & Folders

```
# Copy app _vimrc to user home
# Make directories

Copy-Item C:\tools\vim\_vimrc -Destination $HOME
mkdir ~/.vim 
mkdir ~/.vim/autoload 
mkdir ~/.vim/backup 
mkdir ~/.vim/colors 
mkdir ~/.vim/plugged
```

## Profile

```
# Open, insert mode
vim ~/_vimrc

# Add this: "+p

" Disable compatibility with vi which can cause unexpected issues.
set nocompatible

" Enable type file detection. Vim will be able to try to detect the type of file in use.
filetype on

" Enable plugins and load plugin for the detected file type.
filetype plugin on

" Load an indent file for the detected file type.
filetype indent on

*
* Backup, Undo + Swap files
*
set directory=$HOME/AppData/Local/Temp/
set backupdir=$HOME/AppData/Local/Temp/
set undodir=$HOME/AppData/Local/Temp/

*
* Display
*
* Enable or disable line numbering (number | nonumber).
set number

" Enable syntax highlighting.
syntax on

" Highlight cursor line underneath the cursor horizontally.
set cursorline

" Highlight cursor line underneath the cursor vertically.
set cursorcolumn

" Do not let cursor scroll below or above N number of lines when scrolling.
set scrolloff=10

" Do not wrap lines. Allow long lines to extend as far as the line goes.
set nowrap

*
* Tabs + Spaces
*
" Set shift width to 4 spaces.
set shiftwidth=4

" Set tab width to 4 columns.
set tabstop=4

" Use space characters instead of tabs.
set expandtab

* 
* Mode + Command
* 
" Show the mode you are on the last line.
set showmode

" Show partial command you type in the last line of the screen.
set showcmd

" Set the commands to save in history default number is 20.
set history=1000

*
* Search
*
" Show matching words during a search.
set showmatch

" Use highlighting when doing a search.
set hlsearch

" While searching though a file incrementally highlight matching characters as you type.
set incsearch

" Ignore capital letters during search.
set ignorecase

" Allow earch specifically for capital letters.
set smartcase

*
* Bash Completion
*
" Enable auto completion menu after pressing TAB.
set wildmenu

" Make wildmenu behave like similar to Bash completion.
set wildmode=list:longest

" There are certain files that we would never want to edit with Vim.
" Wildmenu will ignore files with these extensions.
set wildignore=*.docx,*.jpg,*.png,*.gif,*.pdf,*.pyc,*.exe,*.flv,*.img,*.xlsx

*
* Vim wildmenu
*
" Enable auto completion menu after pressing TAB.
set wildmenu

" Make wildmenu behave like similar to Bash completion.
set wildmode=list:longest

" There are certain files that we would never want to edit with Vim.
" Wildmenu will ignore files with these extensions.
set wildignore=*.docx,*.jpg,*.png,*.gif,*.pdf,*.pyc,*.exe,*.flv,*.img,*.xlsx

```

## Basic Commands

```
# New or open
vim flename.ext

# Insert mode
i 

# Command mode
Esc

# Paste from system clipboard (Command)
"+p

# Exit editing, quit, save
Esc 
:wq 
```
