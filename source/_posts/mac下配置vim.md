---
title: mac下配置vim
date: 2020-10-28 22:46:40
tags:
    - vim
    - 利器
categories:
    - mac
---

# mac 下配置 vim

{% asset_img 2020-10-29-04-23-07.png %}

## 安装 mac vim

mac 预装了 vim，但官方的 vim 在 Mac 上只有一个很不完善的，长期没人维护的 Carbon 图形用户界面。macvim 主要是在此基础上添加了一个完整的 Cocoa 用户界面，其核心部分和 vim 同步。MacVim 采用了分离进程的方式，一个 MacVim 程序可以启动多个 vim 进程，每个显示在一个 MacVim 窗口中，这是官方的 vim 和其他平台下的 gvim 所不支持的。MacVim 还支持很多 Mac OS X 原生的界面特性，比如工具栏、滚动条、全屏显示、Mac 菜单快捷键的绑定等。

## 安装 vim-plug

### 下载

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

### 配置

新建一个目录用于存放下载的插件

```
mkdir ~/.vim/plugged
```

## vim-plug 的基本使用

vim-plug 安装后，下面介绍下如何使用：

1、先编辑~/.vimrc 文件，键入如下内容:

```
call plug#begin('~/.vim/plugged')
Plug 'scrooloose/nerdtree'
call plug#end()
```

2、终端键入 vim,然后回车，进入 vim 编辑器，键入如下内容进入命令模式，然后回车:

```
:PlugInstall
```

**Issue:**

由于中文网络环境的某种原因，您可能会遇到vim-plug插件无法下载的问题: vim-plug它依赖github上的开源软件，而github常常无法被正常访问。

解决之法有二:

其一，给命令行挂代理。此法相对复杂，并非简单执行一行命令就完事。提供下思路: 先开启某科学上网的socks5代理或自己ssh -D上自己的机场，使用privoxy转为http代理，然后执行`export http_proxy=http://127.0.0.1:8118`。
其二，通过修改Plug.vim文件中的下载地址为国内的镜像站。

```
vi  ~/.vim/autoload/plug.vim 
```

在 plug.vim 中搜索 github, 修改两条语句，即可成功下载GitHub资源。 

第一部分:

  将
  ```
let fmt = get(g:, 'plug_url_format', 'https://git::@github.com/%s.git')
  ```
  改为
 ``` 
 let fmt = get(g:, 'plug_url_format', 'https://git::@hub.fastgit.org/%s.git')
 ``` 

 第二部分:

  将
 ``` 
\ '^https://git::@github\.com', 'https://github.com', '')
 ``` 
 改为
```
\ '^https://git::@hub.fastgit\.org', 'https://hub.fastgit.org', '')
```

> 参考: https://blog.csdn.net/htx1020/article/details/114364510




**常用的命令:**

查看插件安装状态:

```
:PlugStatus
```

删除插件的方法:

1. 先注释~/.vimrc 文件中插件

```
" Plug 'scrooloose/nerdtreee'
```

2)重启 vim 编辑器

~或者~

~执行:~

```
source ~/.vimrc
```
> 更新:
> 编辑完vimrc之后用
>
>source /etc/vimrc报错
>
>-bash: /etc/vimrc: line 15: syntax error near unexpected token `"autocmd"'
>-bash: /etc/vimrc: line 15: `if has("autocmd")'
>
>以为是语法问题
>
>实际原因：vimrc是vim起动时解释的，而不是由shell来解释。用shell来source它肯定不行。

最后执行:

```
:PlugClean
```

## 安装 molokai 主题

```
cd ~/.vim/
```

```
git clone https://github.com/tomasr/molokai.git
```

```
cd molokai
cp -r colors ../
```

## 配置 Java 环境

### 代码自动补全

**issue:**
`怎么让Mac里的vim支持python3?`
2019 年后 brewhome 安装的和升级 vim 都支持 python3，你可以先升级 brewhome

```
brew update
```

在重新安装或者直接升级

```
brew install vim
```

或者

```
brew upgrade
```

重新启动终端后就可以查看支持 python3

```
vim --version ｜grep python
```

温馨提示：将鼠标放在语句上可以显示对应的英文。 或者 切换至中英文显示
在具有+python3 支持的 Mac OS 上安装 Vim 时，出现以下错误：

% brew install vim --with-python3
Usage: brew install [options] formula

...

Error: invalid option: --with-python3
macos vim homebrew
1 个回复
按投票数排序按时间排序
===============>>#1 票数：0
自 2019 年起，默认情况下已通过 Homebrew 在 Vim 上启用 +python3 。互联网上较旧的说明不准确（为了避免提高搜索排名，我不会链接到它们）。

只需通过 Homebrew 安装/升级：

% brew uninstall vim # optional, only if you previously installed it
% brew install vim
验证+python3 ：

% /usr/local/bin/vim --version
...
+cmdline_compl +lambda +profile +virtualedit
+cmdline_hist +langmap -python +visual
+cmdline_info +libcall +python3 +visualextra
+comments +linebreak +quickfix +viminfo
+conceal +lispindent +reltime +vreplace
...
注意，您仍然会安装系统默认的 Vim，并且可能在安装 Homebrew 版本之前的 PATH 中，因此，如果执行 vim --version ，它不会显示+python3 ：

尝试重新启动终端
尝试 alias vim=/usr/local/bin/vim
试调整 PATH 变量，将 Homebrew 的 Vim 放在首位。 “ 在 Mac 上编辑 PATH 变量 ”具有其他信息。

> 如果系统是ubuntu,安装openjdk8命令如下:
> sudo apt-get install openjdk-8-jdk

## vim 配置文件

```

"==========================================
" 基本设置
"==========================================




"默认文件夹
cd  /Users/admin/Documents/prj/develop



" 编码
set encoding=utf-8


" 不和 vi 兼容
set nocompatible


" 备份和撤销文件
set nobackup
set noswapfile


if has('persistent_undo')
  set undofile
  set undodir=~/.vim/undodir
  if !isdirectory(&undodir)
    call mkdir(&undodir, 'p', 0700)
  endif
endif


" 中文
set fileencodings=ucs-bom,utf-8,gb18030,latin1,gbk,gb2312,cp936
set termencoding=utf-8


" 鼠标支持
if has('mouse')
  if has('gui_running') || (&term =~ 'xterm' && !has('mac'))
    set mouse=a
  else
    set mouse=nvi
  endif
endif

" 共享剪贴板
set clipboard=unnamed


" 设置文本菜单
if has('gui_running')
  let do_syntax_sel_menu = 1
  let do_no_lazyload_menus = 1
endif


if !has('gui_running')
  if has('wildmenu')
    set wildmenu
    set cpoptions-=<
    set wildcharm=<C-Z>
    nnoremap <F10>      :emenu <C-Z>
    inoremap <F10> <C-O>:emenu <C-Z>
  endif
endif


" 查找
set ignorecase
set smartcase
set hlsearch
set incsearch


nnoremap <silent> <F2>      :nohlsearch<CR>
inoremap <silent> <F2> <C-O>:nohlsearch<CR>


set scrolloff=1












"==========================================
"  显示设置
"==========================================
" 显示行号
set number


" 设置缩进
set shiftwidth=2
set tabstop=2


" 突出显示当前行
set cursorline


" 启动 vim 时关闭折叠代码
set nofoldenable


" 主题
syntax enable
colorscheme molokai
set background=dark


" 背景透明
hi Normal ctermfg=252 ctermbg=none


" 将字体设置为Meslo LG S DZ Regular for Powerline 13号大小
set guifont=Meslo\ LG\ S\ DZ\ Regular\ for\ Powerline:h13


set laststatus=2                " 必须设置为2,否则状态栏不显示
set t_Co=256                    " 开启256颜色之后，colorschema在vim里好看了许多












"==========================================
" vim-plug
"==========================================


call     plug#begin('~/.vim/plugged')



" -----------------------------------------------
" 代码自动补全
" -----------------------------------------------
"  java https://github.com/artur-shaik/vim-javacomplete2
Plug 'artur-shaik/vim-javacomplete2'

" -----------------------------------------------
" 美化
" -----------------------------------------------
" 配色方案.
Plug 'jakwings/vim-colors'
Plug 'flazz/vim-colorschemes'
" theme color
Plug 'tomasr/molokai' | Plug 'altercation/solarized'


" 彩虹括号
Plug 'luochen1990/rainbow'




" -----------------------------------------------
" 树形目录
" -----------------------------------------------
Plug 'scrooloose/nerdtree'                            " https://github.com/preservim/nerdtree
Plug 'jistr/vim-nerdtree-tabs'                        " https://github.com/jistr/vim-nerdtree-tabs
Plug 'Xuyuanp/nerdtree-git-plugin'                    " https://github.com/Xuyuanp/nerdtree-git-plugin
Plug 'ryanoasis/vim-devicons'                         " https://github.com/ryanoasis/vim-devicons
Plug 'tiagofumo/vim-nerdtree-syntax-highlight'        " https://github.com/tiagofumo/vim-nerdtree-syntax-highlight
Plug 'tpope/vim-eunuch'                               " https://github.com/tpope/vim-eunuch


map <C-n> :NERDTreeToggle<CR>


let NERDTreeMinimalUI = 1
let NERDTreeShowHidden = 1


let g:nerdtree_tabs_open_on_console_startup = 1


" 开发的过程中，我们希望git信息直接在NERDTree中显示出来， 和Eclipse一样，修改的文件和增加的文件都给出相应的标注， 这时需要安装的插件就是 nerdtree-git-plugin,配置信息如下
let g:NERDTreeGitStatusIndicatorMapCustom = {
    \ "Modified"  : "✹",
    \ "Staged"    : "✚",
    \ "Untracked" : "✭",
    \ "Renamed"   : "➜",
    \ "Unmerged"  : "═",
    \ "Deleted"   : "✖",
    \ "Dirty"     : "✗",
    \ "Clean"     : "✔︎",
    \ "Unknown"   : "?"
    \ }


" i 在新的水平分割的窗口中打开
" s 在新的竖直分割的窗口中打开
" t 在标签页中打开
" go 预览文件
" r 刷新光标所在的目录
" R 刷新当前根路径
" I 显示隐藏文件
" m 显示文件操作菜单
" C 将根路径设置为光标所在的目录
" u 设置上级目录为根路径
" gT 前一个 tab
" gt 后一个 tab
" <C-W> 加方向键（h、j、k、l、<Left> 等）可以在窗口之间跳转
" <C-W> + w 跳转到下一个窗口
" <C-W> + s 和 :split 作用相同，把当前窗口横向一分为二
" <C-W> + v 和 :vsplit 作用相同，把当前窗口纵向一分为二
" <C-W> + o 或 :only 只保留当前窗口，关闭其他所有窗口




" 组合快捷键：nnoremap是什么意思？——简单来说，nnoremap将一个组合快捷键映射为另一个快捷键。no部分，指的是在Vim的正常模式（Normal Mode）下，而不是可视模式下重新映射
nnoremap <C-J> <C-W><C-J>    " 组合快捷键：- Ctrl-j 切换到下方的分割窗口
nnoremap <C-K> <C-W><C-K>    " 组合快捷键：- Ctrl-k 切换到上方的分割窗口
nnoremap <C-L> <C-W><C-L>    " 组合快捷键：- Ctrl-l 切换到右侧的分割窗口
nnoremap <C-H> <C-W><C-H>    " 组合快捷键：- Ctrl-h 切换到左侧的分割窗口
















" -----------------------------------------------
" 标签
" -----------------------------------------------
Plug 'majutsushi/tagbar'                         " https://github.com/majutsushi/tagbar


nnoremap <F9>      :TagbarToggle<CR>
inoremap <F9> <C-O>:TagbarToggle<CR>


let g:tagbar_type_typescript = {
  \ 'ctagsbin' : 'tstags',
  \ 'ctagsargs' : '-f-',
  \ 'kinds': [
    \ 'e:enums:0:1',
    \ 'f:function:0:1',
    \ 't:typealias:0:1',
    \ 'M:Module:0:1',
    \ 'I:import:0:1',
    \ 'i:interface:0:1',
    \ 'C:class:0:1',
    \ 'm:method:0:1',
    \ 'p:property:0:1',
    \ 'v:variable:0:1',
    \ 'c:const:0:1',
  \ ],
  \ 'sort' : 0
\ }












" -----------------------------------------------
" 自动补全
" -----------------------------------------------
Plug 'Valloric/YouCompleteMe'                         " https://github.com/ycm-core/YouCompleteMe
Plug 'Raimondi/delimitMate'                           " https://github.com/Raimondi/delimitMate


nnoremap <Leader>fi :YcmCompleter FixIt<CR>
nnoremap <Leader>gd :YcmCompleter GoToDefinition<CR>












" -----------------------------------------------
" 语法高亮，检查
" -----------------------------------------------
Plug 'sheerun/vim-polyglot'                           " https://github.com/sheerun/vim-polyglot
Plug 'w0rp/ale'                                       " https://github.com/w0rp/ale


let g:ale_fix_on_save = 1
let g:ale_sign_column_always = 1
let g:ale_sign_error = '●'
let g:ale_sign_warning = '▶'


nmap <silent> <C-k> <Plug>(ale_previous_wrap)
nmap <silent> <C-j> <Plug>(ale_next_wrap)










" -----------------------------------------------
" 全局搜索，快速打开文件
" -----------------------------------------------
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }   " https://github.com/junegunn/fzf
Plug 'junegunn/fzf.vim'                               " https://github.com/junegunn/fzf.vim
Plug 'ctrlpvim/ctrlp.vim'                             " https://github.com/ctrlpvim/ctrlp.vim


let g:ctrlp_user_command = ['.git', 'cd %s && git ls-files -co --exclude-standard']
let g:ctrlp_regexp = 1


" <c-r>: 切换匹配模式
" <c-t>：在新的 tab 中打开
" :Rg












" -----------------------------------------------
" 状态条
" -----------------------------------------------
Plug 'vim-airline/vim-airline'                        " https://github.com/vim-airline/vim-airline
Plug 'vim-airline/vim-airline-themes'                 " https://github.com/vim-airline/vim-airline-themes


let g:airline_theme='molokai'
let g:airline_powerline_fonts = 1


let g:airline#extensions#tabline#enabled = 1
let g:airline#extensions#tabline#formatter = 'unique_tail'
let g:airline#extensions#ale#enabled = 1
let g:airline#extensions#tabline#buffer_nr_show = 1
let g:airline#extensions#nerdtree_statusline = 0
let g:airline_section_c = ''












" -----------------------------------------------
" 注释
" -----------------------------------------------
Plug 'scrooloose/nerdcommenter'                       " https://github.com/preservim/nerdcommenter


let g:NERDSpaceDelims = 1
let g:NERDDefaultAlign = 'left'


" <leader>c<space> 注释/取消注释
" <leader>ca 切换　// 和 /* */
" <leader>cs /* 块注释 */
" <leader>cm 只用一组符号注释
" <leader>cA 在行尾添加注释












" -----------------------------------------------
" git
" -----------------------------------------------
Plug 'airblade/vim-gitgutter'                         " https://github.com/airblade/vim-gitgutter
Plug 'tpope/vim-fugitive'                             " https://github.com/tpope/vim-fugitive
Plug 'tpope/vim-rhubarb'                              " https://github.com/tpope/vim-rhubarb


set updatetime=100
let g:gitgutter_max_signs = -1


" jump to next hunk: ]c
" jump to previous hunk: [c
" stage the hunk: <Leader>hs
" undo the hunk: <Leader>hu
" preview the hunk: <Leader>hp


" :G
" :Gvdiffsplit
" :GBrowse
"
" s: 加到暂存区中
" u: 重置加入暂存区的修改
" X: 丢弃修改
" ca: 更改上一次提交
" ce: 使用上一次提交
" =: 切换 diff 显示
" cc: 提交当前暂存区中的文件












" -----------------------------------------------
" Markdown
" -----------------------------------------------
Plug 'iamcco/markdown-preview.nvim', { 'do': 'cd app && yarn install'  }      " https://github.com/iamcco/markdown-preview.nvim


" :MarkdownPreview
" :MarkdownPreviewStop












" -----------------------------------------------
" Emmet
" -----------------------------------------------
Plug 'mattn/emmet-vim'                                " https://github.com/mattn/emmet-vim


let g:user_emmet_leader_key='<C-Z>'


" <C-Z>,












" -----------------------------------------------
" Prettier
" -----------------------------------------------
Plug 'prettier/vim-prettier', { 'do': 'npm install' }     " https://github.com/prettier/vim-prettier


autocmd BufWritePre *.js,*.jsx,*.mjs,*.ts,*.tsx,*.css,*.less,*.scss,*.json,*.graphql,*.md,*.vue,*.yaml,*.html PrettierAsync


" :PrettierAsync












" -----------------------------------------------
" Wakatime
" -----------------------------------------------
Plug 'wakatime/vim-wakatime'                              " https://github.com/wakatime/vim-wakatime












" -----------------------------------------------
" Whitespace
" -----------------------------------------------
Plug 'ntpeters/vim-better-whitespace'                     " https://github.com/ntpeters/vim-better-whitespace


let g:better_whitespace_enabled=1


" :StripWhitespace












call plug#end()


" PlugInstall
" PlugUpdate
" PlugClean
" PlugUpgrade
" PlugStatus
" PlugDiff
" PlugSnapshot













```

**更新: 2022-2-19 04:22:41 版**
```
"==========================================
" 基本设置
"==========================================



"默认文件夹
"cd  /Users/admin/Documents/prj/develop


autocmd FileType java setlocal omnifunc=javacomplete#Complete

" 编码
set enc=utf-8


" 不和 vi 兼容
set nocompatible

" 不要用空格代替制表符
set noexpandtab

" 在行和段开始处使用制表符
set smarttab

" Tab键的宽度
set tabstop=4

" 统一缩进为4
set softtabstop=4
set shiftwidth=4

" 自动缩进
set autoindent
set cindent

" 字符间插入的像素行数目
set linespace=0

" 使回格键（backspace）正常处理indent, eol, start等
set backspace=2

" 在被分割的窗口间显示空白，便于阅读
set fillchars=vert:\ ,stl:\ ,stlnc:\

" 备份和撤销文件
set nobackup
set noswapfile

" 输入的命令显示出来，看的清楚些  
"set showcmd 

" 允许折叠
set foldenable
" 手动折叠
set foldmethod=manual

" 显示中文帮助
" wget https://sourceforge.net/projects/vimcdoc/files/vimcdoc/vimcdoc-2.1.0.tar.gz/download -O vimcdoc-2.1.0.tar.gz
" tar -xzvf vimcdoc-2.1.0.tar.gz
" mv vimcdoc-2.1.0/doc ~/.vim/
if version >= 603

    set helplang=cn

    set encoding=utf-8

endif

if has('persistent_undo')
  set undofile
  set undodir=~/.vim/undodir
  if !isdirectory(&undodir)
    call mkdir(&undodir, 'p', 0700)
  endif
endif


" 中文
set fileencodings=ucs-bom,utf-8,gb18030,latin1
set encoding=utf-8
set termencoding=utf-8

" 鼠标支持
if has('mouse')
  if has('gui_running') || (&term =~ 'xterm' && !has('mac'))
    set mouse=a
  else
    set mouse=nvi
  endif
endif

" 共享剪贴板
set clipboard=unnamed


" 设置文本菜单
if has('gui_running')
  let do_syntax_sel_menu = 1
  let do_no_lazyload_menus = 1
endif


if !has('gui_running')
  if has('wildmenu')
    set wildmenu
    set cpoptions-=<
    set wildcharm=<C-Z>
    nnoremap <F10>      :emenu <C-Z>
    inoremap <F10> <C-O>:emenu <C-Z>
  endif
endif


" 查找
" 搜索忽略大小写
set ignorecase
" 
set smartcase
" 搜索逐字符高亮
set hlsearch
set incsearch


nnoremap <silent> <F2>      :nohlsearch<CR>
inoremap <silent> <F2> <C-O>:nohlsearch<CR>


set scrolloff=1












"==========================================
"  显示设置
"==========================================
" 显示行号
set number

" 设置相对行号
set relativenumber

" 设置缩进
set shiftwidth=2
set tabstop=2


" 突出显示当前行
set cursorline


" 启动 vim 时关闭折叠代码
set nofoldenable


" 主题
syntax enable
colorscheme molokai
set background=dark


" 背景透明
hi Normal ctermfg=252 ctermbg=none


" 将字体设置为Meslo LG S DZ Regular for Powerline 13号大小
set guifont=Meslo\ LG\ S\ DZ\ Regular\ for\ Powerline:h13


set laststatus=2                " 必须设置为2,否则状态栏不显示
set t_Co=256                    " 开启256颜色之后，colorschema在vim里好看了许多








"==========================================
" vim-plug
"==========================================


call     plug#begin('~/.vim/plugged')

" Surround  
Plug 'tpope/vim-surround'  
" python自动补全
" Python autocompletion, go to definition.  
Plug 'davidhalter/jedi-vim'  
" Better autocompletion
Plug 'Shougo/neocomplcache.vim'
if has('python')
" YAPF formatter for Python
Plug 'pignacio/vim-yapf-format'
endif

"  python 自动补全
"  wget https://github.com/rkulla/pydiction/archive/master.zip
"  unzip -q master
"  mv pydiction-master pydiction
"  mkdir -p ~/.vim/tools/pydiction
"  cp -r pydiction/after ~/.vim
"  cp pydiction/complete-dict ~/.vim/tools/pydiction
"
filetype plugin on
let g:pydiction_location = '~/.vim/tools/pydiction/complete-dict'


" -----------------------------------------------
" 代码自动补全 
" -----------------------------------------------
Plug 'Valloric/YouCompleteMe'
" *********************************************
" YCM插件相关
" *********************************************
let g:ycm_autoclose_preview_window_after_completion=1
" 跳转到定义处
map <leader>g :YcmCompleter GoToDefinitionElseDeclaration<CR>
" 默认tab、s-tab和自动补全冲突
let g:ycm_key_list_select_completion = ['<TAB>', '<c-n>', '<Down>']
let g:ycm_key_list_previous_completion = ['<S-TAB>', '<c-p>', '<Up>']
let g:ycm_auto_trigger = 1
"  java https://github.com/artur-shaik/vim-javacomplete2
Plug 'artur-shaik/vim-javacomplete2'
autocmd FileType java setlocal omnifunc=javacomplete#Complete
let g:SuperTabDefaultCompletionType = '<C-x><C-o>'
"inoremap <buffer> <C-X><C-U> <C-X><C-U><C-P>
"inoremap <buffer> <C-S-Space> <C-X><C-U><C-P>
autocmd FileType java set completefunc=javacomplete#CompleteParamsInfo
autocmd FileType java inoremap <expr><CR> pumvisible()?"\<C-Y>":"<CR>"
autocmd FileType java inoremap <buffer> . .<C-X><C-O><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  A  A<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  B  B<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  C  C<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  D  D<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  E  E<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  F  F<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  G  G<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  H  H<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  I  I<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  J  J<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  K  K<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  L  L<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  M  M<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  N  N<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  O  O<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  P  P<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  Q  Q<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  R  R<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  S  S<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  T  T<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  U  U<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  V  V<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  W  W<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  X  X<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  Y  Y<C-N><C-P>
autocmd Filetype java,javascript,css,html,xml inoremap <buffer>  Z  Z<C-N><C-P>


" ==========================================
" LSP 补全方式
"
" 需要额外安装language server, 如果不想要自己可以注释掉
"
"  参考: https://web.archive.org/web/20211021091123/https://www.kawabangga.com/posts/3745
"
"  memo: 没有生效，暂时弃坑!
" 
" ===================================================== 
"Plug 'prabirshrestha/async.vim'
"Plug 'prabirshrestha/asyncomplete.vim'
"Plug 'prabirshrestha/vim-lsp'
"Plug 'prabirshrestha/asyncomplete-lsp.vim'

"inoremap <expr> <Tab> pumvisible() ? "\<C-n>" : "\<Tab>"
"inoremap <expr> <S-Tab> pumvisible() ? "\<C-p>" : "\<S-Tab>"
"inoremap <expr> <cr> pumvisible() ? "\<C-y>\<cr>" : "\<cr>"
" bash lsp(language server)
" https://github.com/bash-lsp/bash-language-server
" 先在终端执行: npm i -g bash-language-server
" For Vim 8 or later install the plugin prabirshrestha/vim-lsp and add the following configuration to .vimrc:
"if executable('bash-language-server')
"  au User lsp_setup call lsp#register_server({
"        \ 'name': 'bash-language-server',
"        \ 'cmd': {server_info->[&shell, &shellcmdflag, 'bash-language-server start']},
"        \ 'whitelist': ['sh'],
"        \ })
"endif



" -----------------------------------------------
" 美化
" -----------------------------------------------
" 配色方案.
Plug 'jakwings/vim-colors'
Plug 'flazz/vim-colorschemes'
" theme color
Plug 'tomasr/molokai' | Plug 'altercation/solarized'


" 彩虹括号
Plug 'luochen1990/rainbow'




" -----------------------------------------------
" 树形目录
" -----------------------------------------------
Plug 'scrooloose/nerdtree'                            " https://github.com/preservim/nerdtree
Plug 'jistr/vim-nerdtree-tabs'                        " https://github.com/jistr/vim-nerdtree-tabs
Plug 'Xuyuanp/nerdtree-git-plugin'                    " https://github.com/Xuyuanp/nerdtree-git-plugin
Plug 'ryanoasis/vim-devicons'                         " https://github.com/ryanoasis/vim-devicons
Plug 'tiagofumo/vim-nerdtree-syntax-highlight'        " https://github.com/tiagofumo/vim-nerdtree-syntax-highlight
Plug 'tpope/vim-eunuch'                               " https://github.com/tpope/vim-eunuch


map <C-n> :NERDTreeToggle<CR>


let NERDTreeMinimalUI = 1
let NERDTreeShowHidden = 1


let g:nerdtree_tabs_open_on_console_startup = 1


" 开发的过程中，我们希望git信息直接在NERDTree中显示出来， 和Eclipse一样，修改的文件和增加的文件都给出相应的标注， 这时需要安装的插件就是 nerdtree-git-plugin,配置信息如下
let g:NERDTreeGitStatusIndicatorMapCustom = {
    \ "Modified"  : "✹",
    \ "Staged"    : "✚",
    \ "Untracked" : "✭",
    \ "Renamed"   : "➜",
    \ "Unmerged"  : "═",
    \ "Deleted"   : "✖",
    \ "Dirty"     : "✗",
    \ "Clean"     : "✔︎",
    \ "Unknown"   : "?"
    \ }

" open nerdtree with the current file selected
nmap ,t :NERDTreeFind<CR>

" i 在新的水平分割的窗口中打开
" s 在新的竖直分割的窗口中打开
" t 在标签页中打开
" go 预览文件
" r 刷新光标所在的目录
" R 刷新当前根路径
" I 显示隐藏文件
" m 显示文件操作菜单
" C 将根路径设置为光标所在的目录
" u 设置上级目录为根路径
" gT 前一个 tab
" gt 后一个 tab
" <C-W> 加方向键（h、j、k、l、<Left> 等）可以在窗口之间跳转
" <C-W> + w 跳转到下一个窗口
" <C-W> + s 和 :split 作用相同，把当前窗口横向一分为二
" <C-W> + v 和 :vsplit 作用相同，把当前窗口纵向一分为二
" <C-W> + o 或 :only 只保留当前窗口，关闭其他所有窗口




" 组合快捷键：nnoremap是什么意思？——简单来说，nnoremap将一个组合快捷键映射为另一个快捷键。no部分，指的是在Vim的正常模式（Normal Mode）下，而不是可视模式下重新映射
nnoremap <C-J> <C-W><C-J>    " 组合快捷键：- Ctrl-j 切换到下方的分割窗口
nnoremap <C-K> <C-W><C-K>    " 组合快捷键：- Ctrl-k 切换到上方的分割窗口
nnoremap <C-L> <C-W><C-L>    " 组合快捷键：- Ctrl-l 切换到右侧的分割窗口
nnoremap <C-H> <C-W><C-H>    " 组合快捷键：- Ctrl-h 切换到左侧的分割窗口
















" -----------------------------------------------
" 标签
" -----------------------------------------------
Plug 'majutsushi/tagbar'                         " https://github.com/majutsushi/tagbar


nnoremap <F9>      :TagbarToggle<CR>
inoremap <F9> <C-O>:TagbarToggle<CR>


let g:tagbar_type_typescript = {
  \ 'ctagsbin' : 'tstags',
  \ 'ctagsargs' : '-f-',
  \ 'kinds': [
    \ 'e:enums:0:1',
    \ 'f:function:0:1',
    \ 't:typealias:0:1',
    \ 'M:Module:0:1',
    \ 'I:import:0:1',
    \ 'i:interface:0:1',
    \ 'C:class:0:1',
    \ 'm:method:0:1',
    \ 'p:property:0:1',
    \ 'v:variable:0:1',
    \ 'c:const:0:1',
  \ ],
  \ 'sort' : 0
\ }












" -----------------------------------------------
" 自动补全
" -----------------------------------------------
Plug 'Valloric/YouCompleteMe'                         " https://github.com/ycm-core/YouCompleteMe
Plug 'Raimondi/delimitMate'                           " https://github.com/Raimondi/delimitMate


nnoremap <Leader>fi :YcmCompleter FixIt<CR>
nnoremap <Leader>gd :YcmCompleter GoToDefinition<CR>












" -----------------------------------------------
" 语法高亮，检查
" -----------------------------------------------
Plug 'sheerun/vim-polyglot'                           " https://github.com/sheerun/vim-polyglot
Plug 'w0rp/ale'                                       " https://github.com/w0rp/ale


let g:ale_fix_on_save = 1
let g:ale_sign_column_always = 1
let g:ale_sign_error = '●'
let g:ale_sign_warning = '▶'


nmap <silent> <C-k> <Plug>(ale_previous_wrap)
nmap <silent> <C-j> <Plug>(ale_next_wrap)










" -----------------------------------------------
" 全局搜索，快速打开文件
" -----------------------------------------------
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }   " https://github.com/junegunn/fzf
Plug 'junegunn/fzf.vim'                               " https://github.com/junegunn/fzf.vim
Plug 'ctrlpvim/ctrlp.vim'                             " https://github.com/ctrlpvim/ctrlp.vim


let g:ctrlp_user_command = ['.git', 'cd %s && git ls-files -co --exclude-standard']
let g:ctrlp_regexp = 1


" <c-r>: 切换匹配模式
" <c-t>：在新的 tab 中打开
" :Rg












" -----------------------------------------------
" 状态条
" -----------------------------------------------
Plug 'vim-airline/vim-airline'                        " https://github.com/vim-airline/vim-airline
Plug 'vim-airline/vim-airline-themes'                 " https://github.com/vim-airline/vim-airline-themes


let g:airline_theme='molokai'
let g:airline_powerline_fonts = 1


let g:airline#extensions#tabline#enabled = 1
let g:airline#extensions#tabline#formatter = 'unique_tail'
let g:airline#extensions#ale#enabled = 1
let g:airline#extensions#tabline#buffer_nr_show = 1
let g:airline#extensions#nerdtree_statusline = 0
let g:airline_section_c = ''












" -----------------------------------------------
" 注释
" -----------------------------------------------
Plug 'scrooloose/nerdcommenter'                       " https://github.com/preservim/nerdcommenter


let g:NERDSpaceDelims = 1
let g:NERDDefaultAlign = 'left'


" <leader>c<space> 注释/取消注释
" <leader>ca 切换　// 和 /* */
" <leader>cs /* 块注释 */
" <leader>cm 只用一组符号注释
" <leader>cA 在行尾添加注释

" <leader> 默认为 \
" 在vim的普通模式下使用“\cc”， 可以轻松注释掉光标所在的行；
" 在visual模式下，使用这个组合键“\cc”， 可以轻松注释掉选择的代码块。

"  <Esc> 是ESC的意思, 先到行首输入/*，然后ESC，到行尾输入*/, j向下移动一行
" /* 注释该行 */
map =  I/*<space><Esc> A */<Esc>j
map == I#<space><Esc>

"数字 0 的意思是回到当前行的行首， 而 $ 是回到行尾 ， x 是删除一个字符， 所以 xxx 就是删除三个字符
" /* 消除该行注释 */
map - 0xxx$xxxj
map -- I<Esc>xx<Esc> 











" -----------------------------------------------
" git
" -----------------------------------------------
Plug 'airblade/vim-gitgutter'                         " https://github.com/airblade/vim-gitgutter
Plug 'tpope/vim-fugitive'                             " https://github.com/tpope/vim-fugitive
Plug 'tpope/vim-rhubarb'                              " https://github.com/tpope/vim-rhubarb


set updatetime=100
let g:gitgutter_max_signs = -1


" jump to next hunk: ]c
" jump to previous hunk: [c
" stage the hunk: <Leader>hs
" undo the hunk: <Leader>hu
" preview the hunk: <Leader>hp


" :G
" :Gvdiffsplit
" :GBrowse
"
" s: 加到暂存区中
" u: 重置加入暂存区的修改
" X: 丢弃修改
" ca: 更改上一次提交
" ce: 使用上一次提交
" =: 切换 diff 显示
" cc: 提交当前暂存区中的文件












" -----------------------------------------------
" Markdown
" -----------------------------------------------
Plug 'iamcco/markdown-preview.nvim', { 'do': 'cd app && yarn install'  }      " https://github.com/iamcco/markdown-preview.nvim


" :MarkdownPreview
" :MarkdownPreviewStop












" -----------------------------------------------
" Emmet
" -----------------------------------------------
Plug 'mattn/emmet-vim'                                " https://github.com/mattn/emmet-vim


let g:user_emmet_leader_key='<C-Z>'


" <C-Z>,












" -----------------------------------------------
" Prettier
" -----------------------------------------------
Plug 'prettier/vim-prettier', { 'do': 'npm install' }     " https://github.com/prettier/vim-prettier


autocmd BufWritePre *.js,*.jsx,*.mjs,*.ts,*.tsx,*.css,*.less,*.scss,*.json,*.graphql,*.md,*.vue,*.yaml,*.html PrettierAsync


" :PrettierAsync












" -----------------------------------------------
" Wakatime
" -----------------------------------------------
Plug 'wakatime/vim-wakatime'                              " https://github.com/wakatime/vim-wakatime












" -----------------------------------------------
" Whitespace
" -----------------------------------------------
Plug 'ntpeters/vim-better-whitespace'                     " https://github.com/ntpeters/vim-better-whitespace


let g:better_whitespace_enabled=1


" :StripWhitespace





" -----------------------------------------------
" 新文件标题
" -----------------------------------------------

" 侦查文件类型
filetype on
" 载入文件类型插件
filetype plugin on
" 为特定文件类型载入相关缩进文件
filetype indent on
"新建.c,.h,.sh,.java文件，自动插入文件头 

autocmd BufNewFile *.cpp,*.[ch],*.sh,*.java exec ":call SetTitle()" 

""定义函数SetTitle，自动插入文件头 

func SetTitle() 

    "如果文件类型为.sh文件 

    if expand("%:e")  == 'sh' 

        call setline(1,"\#########################################################################") 

        call append(line("."), "\# File Name: ".expand("%")) 

        call append(line(".")+1, "\# Author: DANTE FUNG") 

        call append(line(".")+2, "\# mail: fhlin0611@foxmail.com") 

        call append(line(".")+3, "\# Created Time: ".strftime("%c")) 

        call append(line(".")+4, "\#########################################################################") 

        call append(line(".")+5, "\#!/bin/bash") 

        call append(line(".")+6, "") 

    else 

        call setline(1, "/*************************************************************************") 

        call append(line("."), "    > File Name: ".expand("%")) 

        call append(line(".")+1, "    > Author: DANTE FUNG") 

        call append(line(".")+2, "    > Mail:  fhlin0611@foxmail.com") 

        call append(line(".")+3, "    > Created Time: ".strftime("%c")) 

        call append(line(".")+4, " ************************************************************************/") 

        call append(line(".")+5, "")

    endif

    if expand("%:e") == 'cpp'

        call append(line(".")+6, "#include<iostream>")

        call append(line(".")+7, "using namespace std;")

        call append(line(".")+8, "")

    endif

    if expand("%:e") == 'c'

        call append(line(".")+6, "#include<stdio.h>")

        call append(line(".")+7, "")

    endif

    "新建文件后，自动定位到文件末尾

    autocmd BufNewFile * normal G

endfunc 






call plug#end()


" PlugInstall
" PlugUpdate
" PlugClean
" PlugUpgrade
" PlugStatus
" PlugDiff
" PlugSnapshot

```
