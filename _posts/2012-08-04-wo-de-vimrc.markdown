---
layout: post
title: "我的vimrc"
date: 2012-08-04 17:39
comments: true
categories: Vim
---

网上有很多 vim 套件， 之前一直用 [spf13](http://spf13.com/project/spf13-vim)， 后来又用 janus。
时间久了，就自己琢磨着凑了一套自己的， 在公司里推广了一把，感觉效果不错， 吸引了几个同事转投 vim。
我试着解释下 .vimrc 里面的内容， 虽然很多我也不知道，（估计是哪里拷贝来的，还没来得及研究）

```vim

先看这个Modeline， Modeline 就是指定这个文件的格式，
告诉别人下次用 vim 打开这个文件时候，采用相同的设置。
默认的情况下，vim 会在开头和结尾的 5 行(由 'modelines' 控制)
内查找包含 vim: 或者 vi: 之类内容的行，然后根据这行的内容配置本地变量。
所以这里就是告诉 vim 使用大括号来折叠。

" Modeline {
" vim: set foldmarker={,} foldlevel=0 foldmethod=marker :
" }

下面是 Vundle， 这是一个管理 Vim 插件的工具。
具体的可以参见 https://github.com/gmarik/vundle/
按照它的说明，你就可以轻松的使用各种插件。
常用的插件不多：
1. nerdtree
2. ctrlp
3. snipmate-snippets
4. The-NERD-Commenter
5. vim-fugitive
6. vim-surround
7. vim-rails

我基本就用这几个插件。别的还没发现是什么作用，但是就放在那里了。。。
插件的功能我就不介绍了。网上教程一大堆。

" Vundle {
set nocompatible               " be iMproved
filetype off                   " required!

set rtp+=~/.vim/bundle/vundle/
call vundle#rc()

" let Vundle manage Vundle
" required!
Bundle 'gmarik/vundle'

" My Bundles here:
"
" original repos on github
Bundle 'Lokaltog/vim-easymotion'
" vim-scripts repos
Bundle 'L9'
Bundle 'FuzzyFinder'
" non github repos
Bundle 'git://git.wincent.com/command-t.git'
"--------------------------
" tpope plugins
"--------------------------
" disable pathogen, use vundle instead
"runtime bundle/pathogen/autoload/pathogen.vim
"call pathogen#infect()
"Bundle 'tpope/tpope/vim-pathogen'
Bundle 'tpope/vim-fugitive'
Bundle 'tpope/vim-surround'
Bundle 'tpope/vim-abolish'
Bundle 'tpope/vim-speeddating'
Bundle 'tpope/vim-repeat'
Bundle 'tpope/vim-unimpaired'
Bundle 'tpope/vim-rails'

"--------------------------
" utility
"--------------------------
Bundle 'edsono/vim-matchit'
Bundle 'Raimondi/delimitMate'
Bundle 'greyblake/vim-preview'
Bundle 'vim-scripts/jsbeautify'
Bundle 'vim-scripts/The-NERD-Commenter'
Bundle 'godlygeek/tabular'
Bundle 'nvie/vim-togglemouse'
Bundle 'mattn/zencoding-vim'
" alternative for zencoding
Bundle 'rstacruz/sparkup', {'rtp': 'vim/'}
Bundle 'wojtekmach/vim-rename'
Bundle 'vim-scripts/loremipsum'

"--------------------------
" tool for explorer
"--------------------------
Bundle 'corntrace/bufexplorer'
Bundle 'scrooloose/nerdtree'
Bundle 'majutsushi/tagbar'
Bundle 'kien/ctrlp.vim'
Bundle 'vim-scripts/ctags.vim'
Bundle 'mileszs/ack.vim'

"--------------------------
" filetype syntax highlight
"--------------------------
Bundle 'groenewege/vim-less'
Bundle 'kchmck/vim-coffee-script'
Bundle 'plasticboy/vim-markdown'
Bundle 'depuracao/vim-rdoc'
Bundle 'mamut/vim-css-hex'
Bundle 'git://github.com/chrisbra/color_highlight.git'
"--------------------------
" color scheme
"--------------------------
Bundle 'rickharris/vim-blackboard'
Bundle 'altercation/vim-colors-solarized'
Bundle 'endel/vim-github-colorscheme'
Bundle 'git://github.com/tomasr/molokai.git'

"--------------------------
" snipmate
"--------------------------
Bundle 'garbas/vim-snipmate'
Bundle 'honza/snipmate-snippets'
Bundle 'git://github.com/Shougo/neocomplcache.git'
Bundle 'tomtom/tlib_vim'
Bundle 'MarcWeber/vim-addon-mw-utils'
Bundle 'git://github.com/drmingdrmer/xptemplate.git'
Bundle 'git://github.com/ervandew/supertab.git'


filetype plugin indent on     " required!
"
" Brief help
" :BundleList          - list configured bundles
" :BundleInstall(!)    - install(update) bundles
" :BundleSearch(!) foo - search(or refresh cache first) for foo
" :BundleClean(!)      - confirm(or auto-approve) removal of unused bundles
"
" see :h vundle for more details or wiki for FAQ
" NOTE: comments after Bundle command are not allowed..
" }

这里就是一些基本的配置， 比如 leader 映射成逗号。
主题是用 molokai。每条后面都有基本说明。

" Basic setting {

let mapleader = ","
color molokai
set nocompatible      " Use vim, no vi defaults
set number            " Show line numbers
syntax enable         " Turn on syntax highlighting allowing local overrides
set foldenable

set encoding=utf-8    " Set default encoding to UTF-8"

set shiftwidth=4                " use indents of 4 spaces
set tabstop=4                   " an indentation every four columns
set softtabstop=4               " let backspace delete indent
set expandtab                   " tabs are spaces, not tabs
set nowrap
set autoindent

set hlsearch    " highlight matches
set incsearch   " incremental searching
set ignorecase  " searches are case insensitive...
set smartcase   " ... unless they contain at least one capital letter"

ruler 和 statusline 就是最底下的一堆信息， 比如文件名啊， git的信息啊等等。

if has('cmdline_info')
    set ruler                   " show the ruler
    set rulerformat=%30(%=\:b%n%y%m%r%w\ %l,%c%V\ %P%) " a ruler on steroids
    set showcmd                 " show partial commands in status line and
    " selected characters/lines in visual mode
endif

if has('statusline')
    set laststatus=2

    " Broken down into easily includeable segments
    set statusline=%<%f\    " Filename
    set statusline+=%w%h%m%r " Options
    set statusline+=%{fugitive#statusline()} "  Git Hotness
    set statusline+=\ [%{&ff}/%Y]            " filetype
    set statusline+=\ [%{getcwd()}]          " current dir
    set statusline+=%=%-14.(%l,%c%V%)\ %p%%  " Right aligned file nav info
endif

这里是设置光标所在的这一行的高亮属性。

set cursorline
highlight cursorline term=underline cterm=NONE ctermbg=0 gui=NONE guibg=Grey40
if has("autocmd")
    " current line
    au WinLeave * set nocursorline
    au WinEnter * set cursorline
endif

highlight LineNr ctermbg=black guibg=black
highlight LineNr guifg=grey
highlight LineNr ctermfg=grey

nmap <leader>l :set list!<CR>
nmap <leader>s :set spell!<CR>
set pastetoggle=<leader>p
cmap w!! %!sudo tee > /dev/null %

显示tab为这个小三角符号。

set listchars=tab:▸\ ,eol:¬
highlight NonText guifg=#4a4a59
highlight SpecialKey guifg=#4a4a59

这个函数是说将每行最后的空格删除。我在每次保存的时候调用。

function! <SID>StripTrailingWhitespaces()
    " Preparation: save last search, and cursor position.
    let _s=@/
    let l = line(".")
    let c = col(".")
    " Do the business:
    %s/\s\+$//e
    " Clean up: restore previous search history, and cursor position
    let @/=_s
    call cursor(l, c)
endfunction

autocmd BufWritePre * :call <SID>StripTrailingWhitespaces()

set wildignore+=*.o,*.out,*.obj,.git,*.rbc,*.rbo,*.class,.svn,*.gem
set wildignore+=*.zip,*.tar.gz,*.tar.bz2,*.rar,*.tar.xz
set wildignore+=*/vendor/gems/*,*/vendor/cache/*,*/.bundle/*,*/.sass-cache/*
set wildignore+=*.swp,*~,._*
set wildignore+=*.o,*.obj,.git,*.pyc

set autowrite
set backup
set backupdir=~/.vim/backup    " where to put backup files.
set backupext=.bak
set directory=~/.vim/temp      " where to put swap files.

set sessionoptions=blank,buffers,curdir,folds,tabpages,winsize
autocmd BufReadPost *
            \ if line("'\"") > 1 && line("'\"") <= line("$") |
            \   exe "normal! g`\"" |
            \ endif

" }

" NerdTree {
map <leader>n :NERDTreeToggle<CR>:NERDTreeMirror<CR>
map <leader>e :NERDTreeFind<CR>
nmap <leader>nt :NERDTreeFind<CR>

let NERDTreeShowBookmarks=1
let NERDChristmasTree=1
let NERDTreeIgnore=['\.pyc', '\~$', '\.swo$', '\.swp$', '\.git', '\.hg', '\.svn', '\.bzr']
let NERDTreeChDirMode=0
let NERDTreeQuitOnOpen=1
let NERDTreeShowHidden=1
let NERDTreeKeepTreeInNewTab=1

autocmd WinEnter * call s:CloseIfOnlyNerdTreeLeft()

" Close all open buffers on entering a window if the only
" buffer that's left is the NERDTree buffer
function! s:CloseIfOnlyNerdTreeLeft()
    if exists("t:NERDTreeBufName")
        if bufwinnr(t:NERDTreeBufName) != -1
            if winnr("$") == 1
                q
            endif
        endif
    endif
endfunction
" }

" Fugitive {
nnoremap <silent> <leader>gs :Gstatus<CR>
nnoremap <silent> <leader>gd :Gdiff<CR>
nnoremap <silent> <leader>gc :Gcommit<CR>
nnoremap <silent> <leader>gb :Gblame<CR>
nnoremap <silent> <leader>gl :Glog<CR>
nnoremap <silent> <leader>gp :Git push<CR>
"}

" Tabularize {
if exists(":Tabularize")
    nmap <Leader>a= :Tabularize /=<CR>
    vmap <Leader>a= :Tabularize /=<CR>
    nmap <Leader>a: :Tabularize /:\zs<CR>
    vmap <Leader>a: :Tabularize /:\zs<CR>
endif
"}

" CommandT {
let g:CommandTMaxHeight = 15
"}

" OmniComplete {
if has("autocmd") && exists("+omnifunc")
    autocmd Filetype *
                \if &omnifunc == "" |
                \setlocal omnifunc=syntaxcomplete#Complete |
                \endif
endif
hi Pmenu  guifg=#000000 guibg=#F8F8F8 ctermfg=black ctermbg=Lightgray
hi PmenuSbar  guifg=#8A95A7 guibg=#F8F8F8 gui=NONE ctermfg=darkcyan ctermbg=lightgray cterm=NONE
hi PmenuThumb  guifg=#F8F8F8 guibg=#8A95A7 gui=NONE ctermfg=lightgray ctermbg=darkcyan cterm=NONE
set completeopt=menu,preview,longest

autocmd Filetype c      set omnifunc=ccomplete#Complete
autocmd Filetype xml    set omnifunc=xmlcomplete#CompleteTags
autocmd FileType html set omnifunc=htmlcomplete#CompleteTags
autocmd FileType css set omnifunc=csscomplete#CompleteCSS
autocmd FileType javascript set omnifunc=javascriptcomplete#CompleteJS
autocmd Filetype tex    set omnifunc=syntaxcomplete#Complete"
autocmd FileType python set omnifunc=pythoncomplete#Complete
autocmd FileType php set omnifunc=phpcomplete#CompletePHP
autocmd FileType ruby,eruby set omnifunc=rubycomplete#Complete
autocmd FileType ruby,eruby let g:rubycomplete_buffer_loading = 1
autocmd FileType ruby,eruby let g:rubycomplete_rails = 1
autocmd FileType ruby,eruby let g:rubycomplete_classes_in_global = 1
"}

" neocomplcache {
" Disable AutoComplPop.
let g:acp_enableAtStartup = 0
" Use neocomplcache.
let g:neocomplcache_enable_at_startup = 1
" Use smartcase.
let g:neocomplcache_enable_smart_case = 1
" Use camel case completion.
let g:neocomplcache_enable_camel_case_completion = 1
" Use underbar completion.
let g:neocomplcache_enable_underbar_completion = 1
" Set minimum syntax keyword length.
let g:neocomplcache_min_syntax_length = 3
let g:neocomplcache_lock_buffer_name_pattern = '\*ku\*'

" Define dictionary.
let g:neocomplcache_dictionary_filetype_lists = {
            \ 'default' : '',
            \ 'vimshell' : $HOME.'/.vimshell_hist',
            \ 'scheme' : $HOME.'/.gosh_completions'
            \ }

" Define keyword.
if !exists('g:neocomplcache_keyword_patterns')
    let g:neocomplcache_keyword_patterns = {}
endif
let g:neocomplcache_keyword_patterns['default'] = '\h\w*'

" Plugin key-mappings.
imap <C-k>     <Plug>(neocomplcache_snippets_expand)
smap <C-k>     <Plug>(neocomplcache_snippets_expand)
inoremap <expr><C-g>     neocomplcache#undo_completion()
inoremap <expr><C-l>     neocomplcache#complete_common_string()

" SuperTab like snippets behavior.
"imap <expr><TAB> neocomplcache#sources#snippets_complete#expandable() ? "\<Plug>(neocomplcache_snippets_expand)" : pumvisible() ? "\<C-n>" : "\<TAB>"

" Recommended key-mappings.
" <CR>: close popup and save indent.
inoremap <expr><CR>  neocomplcache#smart_close_popup() . "\<CR>"
" <TAB>: completion.
inoremap <expr><TAB>  pumvisible() ? "\<C-n>" : "\<TAB>"
" <C-h>, <BS>: close popup and delete backword char.
inoremap <expr><C-h> neocomplcache#smart_close_popup()."\<C-h>"
inoremap <expr><BS> neocomplcache#smart_close_popup()."\<C-h>"
inoremap <expr><C-y>  neocomplcache#close_popup()
inoremap <expr><C-e>  neocomplcache#cancel_popup()

" AutoComplPop like behavior.
"let g:neocomplcache_enable_auto_select = 1

" Shell like behavior(not recommended).
"set completeopt+=longest
"let g:neocomplcache_enable_auto_select = 1
"let g:neocomplcache_disable_auto_complete = 1
"inoremap <expr><TAB>  pumvisible() ? "\<Down>" : "\<TAB>"
"inoremap <expr><CR>  neocomplcache#smart_close_popup() . "\<CR>"

" Enable omni completion.
autocmd FileType css setlocal omnifunc=csscomplete#CompleteCSS
autocmd FileType html,markdown setlocal omnifunc=htmlcomplete#CompleteTags
autocmd FileType javascript setlocal omnifunc=javascriptcomplete#CompleteJS
autocmd FileType python setlocal omnifunc=pythoncomplete#Complete
autocmd FileType xml setlocal omnifunc=xmlcomplete#CompleteTags

" Enable heavy omni completion.
if !exists('g:neocomplcache_omni_patterns')
    let g:neocomplcache_omni_patterns = {}
endif
let g:neocomplcache_omni_patterns.ruby = '[^. *\t]\.\w*\|\h\w*::'
"autocmd FileType ruby setlocal omnifunc=rubycomplete#Complete
let g:neocomplcache_omni_patterns.php = '[^. \t]->\h\w*\|\h\w*::'
let g:neocomplcache_omni_patterns.c = '\%(\.\|->\)\h\w*'
let g:neocomplcache_omni_patterns.cpp = '\h\w*\%(\.\|->\)\h\w*\|\h\w*::'
" }

" ctrlp {
let g:ctrlp_working_path_mode = 2
let g:ctrlp_switch_buffer = 2
nnoremap <silent> <C-r> :CtrlPMRU<CR>
nnoremap <silent> <leader>b :CtrlPBuffer<CR>
let g:ctrlp_custom_ignore = {
            \ 'dir':  '\.git$\|\.hg$\|\.svn$',
            \ 'file': '\.exe$\|\.so$\|\.dll$' }
"}

" ColorHighlight {
let g:colorizer_auto_color = 1
let g:colorizer_auto_filetype='css,html'
" }

" GUI for MACVim {
if has("gui_running")
    set go=aAce              " 去掉难看的工具栏和滑动条
    "set transparency=30      " 透明背景
    set guifont=Monaco:h13   " 设置默认字体为monaco
    set showtabline=2        " 开启自带的tab栏
    set columns=140          " 设置宽
    set lines=40             " 设置长
    noremap <D-M-Left> :tabprevious<cr>
    noremap <D-M-Right> :tabnext<cr>
    map <D-1> 1gt
    map <D-2> 2gt
    map <D-3> 3gt
    map <D-4> 4gt
    map <D-5> 5gt
    map <D-6> 6gt
    map <D-7> 7gt
    map <D-8> 8gt
    map <D-9> 9gt
    map <D-0> :tablast<CR>
endif
"}

```

参见我的[vim](https://github.com/lihuazhang/vim)。
