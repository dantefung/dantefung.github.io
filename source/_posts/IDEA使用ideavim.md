---
title: IDEA使用ideavim
date: 2022-03-30 11:11:38
tags:
 - 挖坑待填
 - IDEA
categories:
 - 挖坑待填
 - vim
---

# IDEA使用ideaVim

笔记地址: https://note.youdao.com/web/#/file/recent/markdown/WEB2e6876d533bd4471866b3c31a87e46e7/?search=ideavim

文档配图以及更细致的说明，以后有空再补上:) Have a good day!

## Mac OS 配置文件

**step 1:** 进入用户目录, 例如: /Users/Admin/

**step 2:** 创建配置文件.ideavimrc, 写入内容参见`附录: 配置文件内容` 

```
cd ~
vi .ideavimrc 
```

## Windows 平台配置文件

**step 1:** 进入用户目录, 例如: C:\Users\Administrator

**step 2:** 创建配置文件_ideavimrc, 写入配置内容, 具体内容参见 `附录: 配置文件内容`

## 附录

**配置文件内容:**
```
set sneak
set keep-english-in-normal[-and-restore-in-insert]

let mapleader=' '
" 启用vim surround功能
set surround
" 显示行号
set nu
" 显示相对行号
set rnu
" 高亮搜索
set hlsearch
" 自动定位到输入中的字符串，不需要回车搜索
set incsearch
set ignorecase
set smartcase
" 显示当前的模式
set showmode
set number
" 相对当前行行数显示
" set relativenumber
set scrolloff=3
set history=100000
" 共享系统粘贴板
set clipboard=unnamed

" 开启输入法自动切换功能
set keep-english-in-normal
" 回到insert模式时恢复输入法
set keep-english-in-normal-and-restore-in-insert

" clear the highlighted search result
" (清除高亮)
nnoremap <Leader>sc :nohlsearch<CR>

nnoremap <Leader>fs :w<CR>



" Quit normal mode
" (保存关闭)
nnoremap <Leader>q  :q<CR>
nnoremap <Leader>Q  :qa!<CR>

" Move half page faster
" (上下翻页)
nnoremap <Leader>d  <C-d>
nnoremap <Leader>u  <C-u>

" 快速进入vim模式
inoremap jj <Esc>
inoremap jk <Esc>
inoremap kk <Esc>

" Quit visual mode
vnoremap v <Esc>


" Redo
nnoremap U <C-r>

" 以下是一些vim使用方法 -----------------
" viw  vaw  ciw caw diw daw vi' va' ci' ca' di' da' vi( va( ci( ca( di( da( ...

" ###### vim宏：normal模式下 qr 带表给r标记宏 然后vim操作。  @r 重复一次宏 10@r重复10次宏 注意（idea的提示功能干扰，可以在字符串里面先写好然后在复制黏贴）

" ###### 7.2 替换
"           :s/old/new - 用new替换当前行第一个old。
"           :s/old/new/g - 用new替换当前行所有的old。
"           :n1,n2s/old/new/g - 用new替换文件n1行到n2行所有的old。
"           :%s/old/new/g - 用new替换文件中所有的old。
"           :%s/^/xxx/g - 在每一行的行首插入xxx，^表示行首。
"           :%s/$/xxx/g - 在每一行的行尾插入xxx，$表示行尾。
"           有替换命令末尾加上c，每个替换都将需要用户确认。 如：%s/old/new/gc，加上i则忽略大小写(ignore)。
"           有一种比替换更灵活的方式，它是匹配到某个模式后执行某种命令，
"           法为 :[range]g/pattern/command
"           如 :%g/^ xyz/normal dd。
"           示对于以一个空格和xyz开头的行执行normal模式下的dd命令。
"           于range的规定为：
"           果不指定range，则表示当前行。
"           ,n: 从m行到n行。
"           : 最开始一行（可能是这样）。
"           $: 最后一行
"           .: 当前行
"           %: 所有行

" 以上是一些vim使用方法 -----------------

" 以下是自定义快捷键-----------------

" 复制单个单词到寄存器a并标记到o (使用快捷键 空格+y)
" 注释:（mo => 标记o）,(+yiw =>复制当前单词到系统剪切板),("a => 寄存器a) ,("ayiw => 复制当前单词到寄存器a)
nnoremap <Leader>y mo"+yiw"ayiw

" 剪切单个单词到寄存器a并标记到o (使用快捷键 空格+x)
" 注释:（mo => 标记o[标记为的是使用''来回跳标记]）,(+yiw =>复制当前单词到系统剪切板),("a => 寄存器a) ,("ayiw => 复制当前单词到寄存器a),(diw =>删除当前单词)
nnoremap <Leader>x mo"+yiw"ayiwdiw

" 删除单个字符串并黏贴寄存器a的内容并来回标记o和p  (使用快捷键 空格+v)
" 注释：（mp => 标记p）,(viw => 选中当前单词),（"a => 寄存器a）,(p => 将寄存器a内容黏贴到选中的单词),（'o => 跳回标记o）,('p =>跳回标记p[标记为的是使用''来回跳标记])
nnoremap <Leader>v mpviw"ap'o'p

" idea内置快捷键alt+F1 (使用快捷键 空格+m)
nnoremap <Leader>m :action SelectIn<CR>

" idea内置快捷键control+E (使用快捷键 空格+e)
nnoremap <Leader>e :action RecentFiles<CR>

"生成get set方法  (使用快捷键 空格+cc)
nnoremap <Leader>cc :action Generate<CR>

" shift+h--zz(向上翻页)(使用快捷键 空格+h)
nnoremap <Leader>h <S-h>zz

" shift+l--zz(向下翻页)(使用快捷键 空格+l)
nnoremap <Leader>l <S-l>zz

" 以上是自定义快捷键-----------------

" quit ==> close current window
nnoremap <Leader>q <C-W>w

" Window operation
" (关于窗口操作)
nnoremap <Leader>ww <C-W>w
nnoremap <Leader>wd <C-W>c
nnoremap <Leader>wj <C-W>j
nnoremap <Leader>wk <C-W>k
nnoremap <Leader>wh <C-W>h
nnoremap <Leader>wl <C-W>l
nnoremap <Leader>ws <C-W>s
nnoremap <Leader>w- <C-W>s
nnoremap <Leader>wv <C-W>v
nnoremap <Leader>w\| <C-W>v

" Tab operation
" (切换标签)
nnoremap tn gt
nnoremap tp gT

" ==================================================
" Show all the provided actions via `:actionlist`
" ==================================================

" project search
"相当于IDEA的两次shift按钮
nnoremap <Leader>se :action SearchEverywhere<CR>

"查找用法
nnoremap <Leader>fu :action FindUsages<CR>

"打断点
nnoremap <Leader>bb :action ToggleLineBreakpoint<CR>

"查看所有断点
nnoremap <Leader>br :action ViewBreakpoints<CR>

"DUG启动
nnoremap <Leader>cd :action ChooseDebugConfiguration<CR>

"跳转到Action
nnoremap <Leader>ga :action GotoAction<CR>

"跳转到实体类
nnoremap <Leader>gc :action GotoClass<CR>

"跳转到声明
nnoremap <Leader>gd :action GotoDeclaration<CR>

"跳转到文件
nnoremap <Leader>gf :action GotoFile<CR>

"跳转到实现类
nnoremap <Leader>gi :action GotoImplementation<CR>

"跳转到测试类(没有则自动建立)
nnoremap <Leader>gt :action GotoTest<CR>

"显示当前文件路径
nnoremap <Leader>fp :action ShowFilePath<CR>

"激活maven窗口
"nnoremap <Leader>mv :action ActivateMavenProjectsToolWindow<CR>

"修改所有的关联名字
nnoremap <Leader>re :action RenameElement<CR>

"修改当前文件的文件名
nnoremap <Leader>rf :action RenameFile<CR>

"显示用法
nnoremap <Leader>su :action ShowUsages<CR>

"关闭活动显示板
nnoremap <Leader>tc :action CloseActiveTab<CR>

" ================================================================================================
" 👻👻👻 Which-Key 👻👻👻
" ================================================================================================
 set which-key
 set notimeout

" i: Insert ⭐️
"let g:WhichKeyDesc_InsertAfterBrackets = "<leader>i InsertAfterBrackets"
"nnoremap <Leader>i f(a

" 删除括号内的内容并进入编辑模式
nnoremap <Leader>ci( f(lci(

"以下是不常用

"打开命令管理器
nnoremap <Leader>tl Vy<CR>:action ActivateTerminalToolWindow<CR>
vnoremap <Leader>tl y<CR>:action ActivateTerminalToolWindow<CR>

" built in search looks better
" (查找字符串)
nnoremap / :action Find<CR>

" but preserve ideavim search
" (vim自带的搜索)
nnoremap <Leader>/ /

"添加注释
nnoremap <Leader>;; :action CommentByLineComment<CR>

"改变视图 zen模式等
nnoremap <Leader>cv :action ChangeView<CR>

"跳转到标致
nnoremap <Leader>gs :action GotoSymbol<CR>

"
nnoremap <Leader>ic :action InspectCode<CR>
" 优化导入
nnoremap <Leader>oi :action OptimizeImports<CR>
" 显示悬浮菜单
nnoremap <Leader>pm :action ShowPopupMenu<CR>
" 显示所有的书签.   idea原生命令: 添加书签 ctrl+shift+1,2,3,4....
" 跳转到书签处 ctrl+1,2,3,4,5,6....
nnoremap <Leader>sb :action ShowBookmarks
" 显示类文件结构  eclipse 快捷键为: ctrl+O
nnoremap <Leader>ss :action FileStructurePopup

" 重构相关
" i: Introduce
" extract method/function
vmap <leader>im <action>(ExtractMethod)
" extract constant
vmap <leader>ic <action>(IntroduceConstant)
" extract field
vmap <leader>if <action>(IntroduceField)
" extract variable.   idea快捷键: shift+alt+l  idea补全:  .var
vmap <leader>iv <action>(IntroduceVariable)

"正常启动工程
nnoremap <Leader>rc :action ChooseRunConfiguration<CR>
set clipboard+=ideaput

```