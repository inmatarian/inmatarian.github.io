Inmatarian's Vim Setup
======================

Just for kicks, I replicated my Vim on my Windows8 machine. Here I'll document everything I needed to do to get it working.

## Step 1: Install gVim

It's entirely possible to get by with cygwin, and I also installed vim for cygwin, but I prefer to use the gui version.

## Step 2: vimrc

Theres a lot of things I'm used to with my vimrc config, which I needed to get in place. On windows, it's stored at `~\_vimrc` instead of `.vimrc`, which is an interesting and completely normal thing to know. Just to make this document usable for others, here's the more important parts of my config:

```
" Header ---------------------------------------------------------------------
filetype on
call pathogen#infect() " Load Scripts from .vim/bundle
call pathogen#helptags()
set nocompatible

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

" Indentation Behavior -------------------------------------------------------
set tabstop=4
set softtabstop=4
set shiftwidth=4
set smarttab
set expandtab
set autoindent

" Wildmenu -------------------------------------------------------------------
set wildmenu
set wildmode=list:longest

" GUI ------------------------------------------------------------------------
if has("gui_running")
    set guioptions-=T
    set guioptions+=e
    colorscheme tango2
    set background=dark
    set guifont=DejaVu_Sans_Mono:h10:cANSI
endif

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

" Status Line ----------------------------------------------------------------
set laststatus=2
hi statusline ctermbg=black
set statusline=\ %n:\ %f\ %y%r%m%h%w%<%=%v\,\ %l\/%L\,\ %3P\ 

" Folding --------------------------------------------------------------------
set foldmethod=indent
set foldlevel=99
nn <silent> <space> za

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

There's a small handful of things in here which may not work on windows, like the dictionary set to a fixed unix path. I'll have to find workarounds for this. This document will probably not be updated to reflect that fix; check to see if I have my dotfiles on github.

## Step 3: Themes baby

I prefer the tango2 theme, just because I'm so damn used to tango colors. In that vimrc, there's an area for GUI options where I set the theme and font. Setting the font blew my mind, because I learned how to use the `redir` command to redirect the output of `set guifont?` into a register, since there was no way I could handcraft that command without the aid of the font select dialog (why I opted for the GUI version of vim).


