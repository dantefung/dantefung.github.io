---
title: 从vim迁移到neovim
date: 2022-08-21 00:03:45
tags:
    - vim
---

# 从vim迁移到neovim

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/72bcd9fc868e/)

> 为什么我从 vim 迁移到了 neovim 1 vim 打开大文件时的速度实在太慢, 经常容易卡顿. 毕竟一款上世纪的编辑器了, 速度有些慢是可以理解的, 但是我不能接受. hhhhhh. 而 nvim 打开大文件的速...

### 为什么我从 vim 迁移到了 neovim

*   1 vim 打开大文件时的速度实在太慢, 经常容易卡顿. 毕竟一款上世纪的编辑器了, 速度有些慢是可以理解的, 但是我不能接受. hhhhhh.  
    而 nvim 打开大文件的速度就快多了, 这是导致迁移的一大原因, 还有就是有些插件 neovim 独占, 实在眼馋.
*   2 异步 API, 内嵌完整 terminal
*   3 [neovim 传送门](https://links.jianshu.com/go?to=neovim.io)

### Install

#### mac 安装 neovim

```
brew install neovim
```

#### ubuntu 安装 neovim

*   首先安装 ppa

```
sudo apt-get install software-properties-common
```

*   安装 neovim

```
sudo apt-add-repository ppa:neovim-ppa/unstable
sudo apt-get update
sudo apt-get install neovim
```

*   vim 迁移到 neovim

```
mkdir -p ~/.config/nvim/
cd ~/.config/nvim
cp ~/.vimrc ./init.vim
alias vim=nvim
```

*   更新 vim 插件

```
nvim init.vim

:PlugInstall 等待插件更新完毕
```

*   插件管理，推荐使用 plugvim 替换原来的 vundle. 采用并行方式, 插件安装速度加快

```
mkdir -p ~/.config/nvim/autoload/
curl -fLo ~/.config/nvim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

#### 推荐几个实用插件

*   1 ncm2 自动补全插件

```
Plug 'ncm2/ncm2'
Plug 'ncm2/ncm2-path'
Plug 'ncm2/ncm2-bufword'
Plug 'ncm2/ncm2-jedi'
Plug 'ncm2/ncm2-vim'
```

*   2 有道翻译插件

```
Plug 'ianva/vim-youdao-translater'
"youdao translater
vnoremap <silent> <C-T> :<C-u>Ydv<CR>
nnoremap <silent> <C-T> :<C-u>Ydc<CR>
noremap <leader>yd :<C-u>Yde<CR>  ,leader键加yd可以快速调出翻译
```

*   3 fzf 模糊搜索神器

```
Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all'  }
Plug 'junegunn/fzf.vim'
```

*   4 startify, 在启动 vim 的时候可以显示最近打开的文件

```
Plug 'mhinz/vim-startify'
```

*   5 auto-pairs 补全括号

```
Plug 'jiangmiao/auto-pairs' "auto-pairs
```

*   6 tabular 对齐插件, 可以快速对齐 "=" 号等

```
Plug 'godlygeek/tabular'
:Tab/=
```