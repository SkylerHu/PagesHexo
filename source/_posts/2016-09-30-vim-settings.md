---
title:  如何优雅的使用Vim
date: 2016-09-30 15:33:13
tags:
  - vim
---

## 我的电脑

``` bash
➜  ~  sw_vers
ProductName:    Mac OS X
ProductVersion: 10.10.5
BuildVersion:   14F1021
➜  ~  python -V
Python 2.7.10
```

## 配置前的准备

默认大家是安装了 [HomeBrew](http://brew.sh/), 没有的话请自行安装

若是需要安装Python的代码提示 `YouCompleteMe`, 要求:
* YouCompleteMe unavailable: requires Vim 7.3.598+
* 还需要Python支持Python module

执行以下命令可以查看相关支持信息(`+`支持、`-`不支持)

``` bash
➜  ~  vim --version
VIM - Vi IMproved 7.4 (2013 Aug 10, compiled Apr 28 2016 11:12:36)
+cryptv          +linebreak       -python          +vreplace
+cscope          +lispindent      +python3         +wildignore
```

若是版本不对 或者 not support python module，请执行

``` bash
sudo mv /usr/bin/vim /usr/bin/vim_bk  # 把系统默认的vim备份了
brew install vim --with-python3  # 安装新的vim
# 若是下面的步骤执行不下去了，尝试一下 brew install vim --with-python
```

* 最后还需安装  `brew instal CMake`

## 安装vim 插件

执行以下脚本

``` bash
#!/bin/sh

if [ ! -d ~/.vim ]; then
mkdir ~/.vim
fi

cd ~/.vim

if [ ! -d bundle ]; then
mkdir bundle
fi

cd bundle
git clone https://github.com/VundleVim/Vundle.vim.git   # 管理工具
git clone https://github.com/Lokaltog/vim-powerline.git # 
git clone https://github.com/scrooloose/nerdtree.git # 目录树
git clone https://github.com/jistr/vim-nerdtree-tabs.git  # 
git clone https://github.com/vim-scripts/taglist.vim.git # 方法类跳转
git clone https://github.com/vim-scripts/AutoTag.git
git clone https://github.com/scrooloose/syntastic.git  # js语法检测
git clone https://github.com/tpope/vim-fugitive.git # 显示Git信息
git clone https://github.com/Valloric/YouCompleteMe.git # python 自动补全
git clone https://github.com/hynek/vim-python-pep8-indent # pep8 python语法 需要在项目虚拟环境下 pip install pep8
git clone https://github.com/nvie/vim-flake8 # 代码风格检测 需要在项目虚拟环境下 pip install flake8
git clone https://github.com/Yggdroot/indentLine  # 代码格式对齐线
# git clone https://github.com/vim-ruby/vim-ruby # ruby插件
cd YouCompleteMe
git submodule update --init --recursive
./install.py  --clang-completer

cd ..
mkdir -p ~/.vim/autoload ~/.vim/bundle && curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim
cd ~
brew install ctags
```

以上所有vim插件都在GitHub上
若是要更新哪个插件，直接到插件目录下去git pull 就行了；
值得注意的是 最好查看下插件的README，看它的用发是否发生了改变，需要自行更新 .vimrc中的配置

## .vimrc 配置文件

``` bash
cd ~
vim .vimrc  # 编辑 .vimrc 文件 内容如下
```

``` conf
syntax on
set encoding=utf-8
" 自动缩进
set autoindent
set cindent
set autoread " 设置当文件被改动时自动载入
set wildmenu " 增强模式中的命令行自动完成操作
set showcmd         " 输入的命令显示出来，看的清楚些  
set whichwrap+=<,>,h,l  " 允许backspace和光标键跨越行边界
set ruler           " 显示标尺  
set tabstop=4
set shiftwidth=4
set softtabstop=4
set expandtab
set nu
set hlsearch
set incsearch
set colorcolumn=120
set cul "高亮光标所在行
autocmd InsertEnter * se cul    " 用浅色高亮当前行  
set scrolloff=3     " 光标移动到buffer的顶部和底部时保持3行距离  
"set statusline=%<\ %n:%F\ %m%r%y%=%-35.(line:\ %l\ of\ %L,\ col:\ %c%V\ (%P)%)
set statusline=%F%m%r%h%w\ [FORMAT=%{&ff}]\ [TYPE=%Y]\ [POS=%l,%v][%p%%]\ %{strftime(\"%d/%m/%y\ -\ %H:%M\")}   "状态行显示的内容  
set laststatus=2
set ignorecase
set backspace=indent,eol,start " backspace over everything in insert mode
set nocompatible              " be iMproved, required
filetype off                  " required

set foldnestmax=10      "deepest fold is 10 levels
set foldlevel=1         "this is just what i use
set foldmethod=marker

set paste

map <F2> :mksession! ~/.vim_session <cr> " Quick write session with F2
map <F3> :source ~/.vim_session <cr>     " And load session with F3

execute pathogen#infect()

function! ResCur()
        if line("'\"") <= line("$")
                normal! g`"
                return 1
        endif
endfunction

augroup resCur
        autocmd!
        autocmd BufWinEnter * call ResCur()
augroup END

filetype plugin indent on    " required
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"""""新文件标题
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"新建.c,.h,.sh,.java文件，自动插入文件头 
autocmd BufNewFile *.cpp,*.[ch],*.sh,*.rb,*.java,*.py exec ":call SetTitle()" 
""定义函数SetTitle，自动插入文件头 
func SetTitle() 
	"如果文件类型为.sh文件 
	if &filetype == 'sh' 
		call setline(1,"\#!/bin/bash") 
		call append(line("."), "") 
    elseif &filetype == 'python'
        call setline(1,"#!/usr/bin/env python")
        call append(line("."),"# coding=utf-8")
	    call append(line(".")+1, "") 
	endif
endfunc 
autocmd BufNewFile * normal G

au BufNewFile,BufRead *.yml set filetype=xml
autocmd FileType python setlocal completeopt-=preview

"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"""" 插件管理
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'gmarik/Vundle.vim'
" All of your Plugins must be added before the following line
call vundle#end()            " required

""""""""""""""""""""""""""""""""""""""""""""""""""""""
"Yggdroot/indentLine
let g:indentLine_char = '┊'
""""""""""""""""""""""""""""""""""""""""""""""""""""""
"powerline{
set guifont=PowerlineSymbols\ for\ Powerline
set nocompatible
set t_Co=256
let g:Powerline_stl_path_style = "full"
"}
""""""""""""""""""""""""""""""""""""""""""""""""""""""
"打开文件类型检测, 加了这句才可以用智能补全
set completeopt=preview,menu 
set completeopt=longest,menu
"python补全
let g:pydiction_location = '~/.vim/after/complete-dict'
let g:pydiction_menu_height = 20
let g:miniBufExplMapWindowNavVim = 1
let g:miniBufExplMapWindowNavArrows = 1
let g:miniBufExplMapCTabSwitchBufs = 1
let g:miniBufExplModSelTarget = 1
" ycm 此处要设置成python版本2.7，不要设置成python3了
let g:ycm_path_to_python_interpreter="python"
""""""""""""""""""""""""""""""""""""""""""""""""""""""
"syntax check
set statusline+=%#warningmsg#
set statusline+=%{SyntasticStatuslineFlag()}
set statusline+=%*
let g:syntastic_always_populate_loc_list = 1
let g:syntastic_check_on_open = 1
let g:syntastic_check_on_wq = 0
let g:syntastic_auto_loc_list = 1
let g:syntastic_python_checkers=['flake8']
let g:syntastic_python_flake8_args='--ignore W391,E501 --max-line-length 119'
let g:syntastic_loc_list_height=3
let g:syntastic_enable_highlighting=0
""""""""""""""""""""""""""""""""""""""""""""""""""""""
"ctags
set tags=./tags,tags;
" Tag list (ctags) 
let Tlist_Ctags_Cmd = '/usr/local/bin/ctags' 
let Tlist_Show_One_File = 1 "不同时显示多个文件的tag，只显示当前文件的 
let Tlist_File_Fold_Auto_Close = 1
let Tlist_Exit_OnlyWindow = 1 "如果taglist窗口是最后一个窗口，则退出vim 
let Tlist_Use_Right_Window = 1 "在右侧窗口中显示taglist窗口
nmap tl :Tlist<cr>
""""""""""""""""""""""""""""""""""""""""""""""""""""""
"so that when you preview the new file takes up 80% and the file explorer the other 20%.
let g:netrw_winsize=25
let g:netrw_altv = 2
let g:netrw_browse_split = 3
let g:netrw_keepdir = 0
let NERDTreeWinSize=30
"let g:nerdtree_tabs_open_on_console_startup=1
"列出当前目录文件  
map <F3> :NERDTreeToggle<CR>
imap <F3> <ESC> :NERDTreeToggle<CR>
"当打开vim且没有文件时自动打开NERDTree
autocmd vimenter * if !argc() | NERDTree | endif
" 只剩 NERDTree时自动关闭
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTreeType") && b:NERDTreeType == "primary") | q | endif
" 忽略的文件不加载
set wildignore+=**/fe-dist/**
set wildignore+=**/node_modules/**
set wildignore+=**/log/**
set wildignore+=**/static/**
set wildignore+=*.swp,*.zip,*.pyc,.git/
set path=.,,**
""""""""""""""""""""""""""""""""""""""""""""""""""""""
"Yggdroot/indentLine
let g:indentLine_char = '┊'
""""""""""""""""""""""""""""""""""""""""""""""""""""""
```

## 用 vundle 管理 vim 插件

其实上面脚本一键执行也挺方便的，此处只是稍微介绍一下 vundle 的用法。

可在 `.vimrc`  文件中添加配置

``` conf
Bundle 'cespare/vim-golang'
Bundle 'taglist.vim'
Bundle 'dhruvasagar/vim-table-mode'
Bundle 'godlygeek/tabular'
Bundle 'derekwyatt/vim-scala'
Bundle 'kien/ctrlp.vim'
Bundle 'Valloric/YouCompleteMe'
Bundle 'Yggdroot/indentLine'
```

随便开个窗口，从控制台打开vim，然后执行-> :BundleInstall ，vundle会自动下载声明的插件并安装到 ./vim/bundle 目录里面



** 到此，安装结束，可以畅游vim了。 **
