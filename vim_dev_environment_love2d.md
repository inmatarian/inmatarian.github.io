---
title: Inmatarian's Vim Setup
---

Inmatarian's Vim Setup
======================

Just for kicks, I replicated my Vim on my Windows8 machine. Here I'll document everything I needed to do to get it working. This document will be be good for general purpose development, but I'll be using Vim for Lua/Love2d development and my choice of plugins and configs will probably be in response to making Lua development easier. I imagine that my configs might make it harder to develop in other languages.

## Step 1: Install gVim

It's entirely possible to get by with cygwin, and I also installed vim for cygwin, but I prefer to use the gui version.

Link: http://www.vim.org/download.php#pc

## Step 2: vimrc

Theres a lot of things I'm used to with my vimrc config, which I needed to get in place. On windows, it's stored at `~\_vimrc` instead of `.vimrc`, which is an interesting and completely normal thing to know. Just to make this document usable for others, here's the more important parts of my config.

There's a small handful of things in here which may not work on windows, like the dictionary set to a fixed unix path. I'll have to find workarounds for this. This document will probably not be updated to reflect that fix; check to see if I have my dotfiles on github.

### Pathogen, see plugins below
```
" Header ---------------------------------------------------------------------
filetype on
call pathogen#infect() " Load Scripts from .vim/bundle
call pathogen#helptags()
set nocompatible
```

### Basic stuffs
I consider these to be par for the course. Line numbers, command timeouts, hidden buffers, the works.
```
" Basic Options --------------------------------------------------------------
filetype plugin indent on
syntax on
set encoding=utf-8
set showmode
set showcmd
set hidden
set number
set lazyredraw
set ttyfast
"set list
set listchars=tab:>·
set splitbelow
set splitright
set timeoutlen=3000
set ttimeoutlen=300
set more
" set tildeop
set dictionary+=/usr/share/dict/words
set clipboard=unnamed
set mouse=a
set backspace=2
```

### Indenting Like It's Going Out Of Style
I'm a 4-spaces to a tab kind of guy, though it's interesting to note that the Lua community has settled on 2-spaces to a tab.
```
" Indentation Behavior -------------------------------------------------------
set tabstop=4
set softtabstop=4
set shiftwidth=4
set smarttab
set expandtab
set autoindent
```

### Wild Wild Menus
Another thing that goes without saying. The Wildmenu complements autocompletion with a list of candidates.
```
" Wildmenu -------------------------------------------------------------------
set wildmenu
set wildmode=list:longest
```

### Gooey
See the section on theming
```
" GUI ------------------------------------------------------------------------
if has("gui_running")
    set guioptions-=T
    set guioptions+=e
    colorscheme tango2
    set background=dark
    set guifont=DejaVu_Sans_Mono:h10:cANSI
endif
```

### Makefiles and Lua Tabs
Here's where I say specifically that I'm going to behave when editing Lua files. Also, Makefiles use tab-chars semantically, so I can't replace them with spaces.
```
" File Behavior --------------------------------------------------------------
if has("autocmd")
  augroup prog
    au!
    " Don't expand tabs to spaces in Makefiles
    au BufEnter  [Mm]akefile*  set noet
    au BufLeave  [Mm]akefile*  set et

    au FileType lua setlocal tabstop=2 shiftwidth=2 softtabstop=2

  augroup END
end

au BufReadPost *.py set syntax=python " instead of conf
```

### Finding Stuff
I still use arrows for movement. hjkl is the one thing I never quite got a hold of. It makes my fingers feel too cramped in.
```
" Searching and Movement -----------------------------------------------------
set incsearch       " do incremental searching
set hlsearch        " highlight searched
set ignorecase      " ignore case when searching
set scrolloff=1
set sidescrolloff=3
set whichwrap=b,s,h,l,<,>,[,]   " move freely between files
nn <silent> <Cr> :noh<Cr>

" Up and down are more logical with g
nn <silent> k gk
nn <silent> j gj
nn <silent> <Up> gk
nn <silent> <Down> gj
ino <silent> <Up> <C-O>gk
ino <silent> <Down> <C-O>gj
```

### Knowing whats going on
Customized status bar. Looks like this: `1: Kill_All_Humans.txt [txt][+]    75, 113/289, 46%`
```
" Status Line ----------------------------------------------------------------
set laststatus=2
hi statusline ctermbg=black
set statusline=\ %n:\ %f\ %y%r%m%h%w%<%=%v\,\ %l\/%L\,\ %3P\ 
```

### Getting stuff out of my face
The indent method works pretty well. The only surprise here is that I use the spacebar for opening and closing indents.
```
" Folding --------------------------------------------------------------------
set foldmethod=indent
set foldlevel=99
nn <silent> <space> za
```

### My own shortcuts
These are more for my own usage. Anyone else who tries using these may find that they don't work for them. The Alt+Arrow shortcuts for navigating Vim-Windows are pretty useful though.
```
" Key Mappings ---------------------------------------------------------------
nmap <F1> <nop>
imap <F1> <nop>
nn <silent> <F2> <esc>:NERDTreeToggle<cr>
nn <silent> <Leader><F2> <esc>:NERDTreeFind<cr>
nn <silent> <Leader><F3> <esc>:TlistToggle<cr>
nn <silent> <F3> <esc>n
nn <silent> <F4> <esc>:BuffergatorToggle<cr>
nn <silent> <F6> :e %:p:s,.h$,.X123X,:s,.cpp$,.h,:s,.X123X$,.cpp,<CR>
nn <silent> <F12> :bn!<Cr>
nn <silent> <Leader><F12> :bp!<Cr>
nn <silent> <leader>ev :e ~/.vimrc<cr>
nn <silent> <leader>v :so ~/.vimrc<cr>:echo ".vimrc reloaded"<cr>
nn ; :
nn <silent> , ;
nn Q gq
nn <silent> <Leader>t :r !date<cr>
nn <C-s> <esc>:w<cr>

" Pastemode Stuff
map <silent> <F8> :set paste<cr>
map <silent> <S-F8> :set nopaste<cr>
imap <silent> <F8> <C-O>:set paste<cr>
imap <silent> <S-F8> <nop>
set pastetoggle=<S-F8>

"make Y behave more like C and D
nmap Y y$
" \d to delete without storing in a register.
nn <silent> <Leader>d "_d
nn <silent> <Leader>D "_D
vn <silent> <Leader>d "_d
vn <silent> <Leader>D "_D
" \p to paste what was recently yanked ignoring deletes.
nn <silent> <Leader>p "0p
nn <silent> <Leader>P "0P
" Tildeops is magic, but let's get around the voodoo for a second.
nn <silent> <Leader>~ g~l
nn <silent> <Leader>l <esc>:set list!<CR>
" Change directory more easily
nn <Leader>cd :cd %:p:h<CR>:pwd<CR>

" window switching like weechat
nn <silent> <M-Left> <esc><C-W><Left>
nn <silent> <M-Right> <esc><C-W><Right>
nn <silent> <M-Up> <esc><C-W><Up>
nn <silent> <M-Down> <esc><C-W><Down>
nn <silent> <Leader><Left> <esc><C-W><Left>
nn <silent> <Leader><Right> <esc><C-W><Right>
nn <silent> <Leader><Up> <esc><C-W><Up>
nn <silent> <Leader><Down> <esc><C-W><Down>
```

### Other stuffs
Because I mipsell things all the thyme.
```
" Aliases for common typos ---------------------------------------------------
cnoreabbrev E e
cnoreabbrev W w
cnoreabbrev WQ wq
cnoreabbrev Wa wa
cnoreabbrev WA wa
cnoreabbrev Wall wall
cnoreabbrev WAll wall
cnoreabbrev WALL wall
cnoreabbrev Q q
cnoreabbrev QA qa

" Preventing Spam ------------------------------------------------------------
set suffixes+=.bak
set suffixes+=.swp
set suffixes+=.o
set suffixes+=.class
set suffixes+=.pyc
set suffixes+=.pyo
set suffixes+=.png
set suffixes+=.jpg
set suffixes+=.gif
set suffixes+=.bmp

" swap files make me a sad panda
if has("win32") || has("win64")
   set directory=$TMP
else
   set directory=/tmp
end
```

## Step 3: Themes baby

I prefer the tango2 theme, just because I'm so damn used to tango colors. In that vimrc, there's an area for GUI options where I set the theme and font. Setting the font blew my mind, because I learned how to use the `redir` command to redirect the output of `set guifont?` into a register, since there was no way I could handcraft that command without the aid of the font select dialog (why I opted for the GUI version of vim).

## Step 4: Plugins

### Pathogen

Start with pathogen. Install the `pathogen.vim` file in `~\vimfiles\autoload\` (the vimfiles in another interesting detail). That should make the rest of this install painless and easy. The top of the vimrc has the infect and helptags calls in place.

To install the rest of the plugins, we can just git-clone the repos into the `~\vimfiles\bundle\REPONAMEHERE` location, which is very handy.

Link: https://github.com/tpope/vim-pathogen

### NERDTree

File tree-listing for quick loading of other files to edit. Crucial if you ask me. The vimrc config:
```
" -- NERDTree
let NERDTreeIgnore = []
for suffix in split(&suffixes, ',')
  let NERDTreeIgnore += [ escape(suffix, '.~') . '$' ]
endfor
let NERDTreeWinSize = 19
```

Link: https://github.com/scrooloose/nerdtree

### Buffergator

Quick way to switch between hidden buffers. This is one of those things that trips up people when using vim, since they expect vim Tabs to work the way Tabs work in other editors. Hidden Buffers plus either bufferexplorer or buffergator brings a tab surrogate.

Vimrc config:
```
" -- Buffer Gator
let g:buffergator_viewport_split_policy="T"
let g:buffergator_autoexpand_on_split=0
let g:buffergator_split_size=10
let g:buffergator_display_regime='filepath'
```

Link: https://github.com/jeetsukumaran/vim-buffergator

### Supertab

Vim's builtin omnicompleter is alright for most usages (it's no intellisense), but the shortcut keys to use it are a pain. This plgun gives us an easy tab-complete behavior in the insert mode.

Vimrc config:
```
" -- SuperTab
let g:SuperTabDefaultCompletionType = "context"
```

Link: https://github.com/ervandew/supertab

