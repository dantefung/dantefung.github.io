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

#### windows 安装 neovim

#### 安装
- 前置条件（一般在linux上大多要点是天然满足的）：
- 正常的网络环境
- 安装有neovim
- 拥有c/c++工具链
- neovim构建时包含LuaJIT支持
- neovim构建时包含python支持
- 安装有Nerd Font系列字体
- 终端支持真色彩和下划线显示

https://github.com/neovim/neovim/blob/master/INSTALL.md

---

Install from download
=====================

Downloads are available on the [Releases](https://github.com/neovim/neovim/releases) page.

* Latest [stable release](https://github.com/neovim/neovim/releases/latest)
    * [macOS x86_64](https://github.com/neovim/neovim/releases/latest/download/nvim-macos-x86_64.tar.gz)
    * [macOS arm64](https://github.com/neovim/neovim/releases/latest/download/nvim-macos-arm64.tar.gz)
    * [Linux x86_64](https://github.com/neovim/neovim/releases/latest/download/nvim-linux-x86_64.tar.gz)
    * [Linux arm64](https://github.com/neovim/neovim/releases/latest/download/nvim-linux-arm64.tar.gz)
    * [Windows](https://github.com/neovim/neovim/releases/latest/download/nvim-win64.msi)
* Latest [development prerelease](https://github.com/neovim/neovim/releases/nightly)

[点我](https://github.com/neovim/neovim/releases/tag/v0.11.0)
[安装包](https://github.com/neovim/neovim/releases/download/v0.11.0/nvim-win64.zip)

> Windows
> *Zip*
> 
> Download nvim-win64.zip
> Extract the zip
> Run nvim.exe on your CLI of choice
> 
> *MSI*
> Download nvim-win64.msi
> Run the MSI
> Run nvim.exe on your CLI of choice
> Note: On Windows "Server" you may need to install vcruntime140.dll.

安装 `nvim-win64.msi`, 然后一直下一步即可. 

{% asset_img 2025-04-03-17-42-55.png %}

等会按 `q`

{% asset_img 2025-04-03-17-53-47.png %}

1. 打开git-bash
2. 新增别名
```
vi ~/.bashrc
```
写入:
```
alias vim="D:/software/develop/nvim-win64/nvim-win64/bin/nvim.exe"
```
重新载入.bashrc
```
source ~/.bashrc
```
3. 命令行键入 `vim`, 即可开始简单愉快使用neovim了.

#### windows迁移配置

```
curl -fLo ~/AppData/Local/nvim/autoload/plug.vim  --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

#### windows结合lazyVim

- [LazyVim for Ambitious Developers](https://lazyvim-ambitious-devs.phillips.codes/course/chapter-1/)

*第一步*:

```
git clone https://github.com/LazyVim/starter.git
```

*第二步*:

这个仓库中最重要的就是 `lua 文件夹`和 `init.lua`(这个就是初始化文件) 将其他文件删除

将这两个文件移动到 `~/AppData/Local/nvim/` 目录下

> 

*第三步*:

```
vim
```

{% asset_img 2025-04-03-17-41-21.png %}

> 注意:  这里的vim命令是 `nvim`, 因为用户的配置文件里边改了别名: alias vim=/{你的安装路径}/nvim
>
> Neovim 从 `0.5` 版本开始，已经内置了 **LuaJIT**（高性能 Lua 解释器），可以直接执行 Lua 脚本。这意味着：
> - **无需单独安装 Lua**：Neovim 的安装包已经包含完整的 Lua 环境。
> - **插件和配置直接调用内置 Lua**：所有 Lua 代码（如 LazyVim 的配置文件）都由 Neovim 自身的 Lua 运行时解析。


*第四步*:

Linux 和 Mac OS 这里应该不会有问题。

对于 Windows，因为 `Treesitter` 需要 C 语言编译器编译一些语法解析器，所以我们需要装一个 C 编译器.

[x86_64-14.2.0-release-win32-seh-ucrt-rt_v12-rev0.7z](https://github.com/niXman/mingw-builds-binaries/releases/download/14.2.0-rt_v12-rev0/x86_64-14.2.0-release-win32-seh-ucrt-rt_v12-rev0.7z)，直接点击就可以下载到浏览器下载目录下。如果有其他需要，也可以下载其他的压缩包，其中：

- `i686` 是 32 位版本，x86_64 是 64 位版本

- `win32` 适用于 windows 操作系统，posix 适用于其他操作系统

- `dwarf` 是 32 位的异常处理系统，seh 适用于 64 位

- `msvcrt` 是旧版微软 C 运行时库，ucrt 是新版运行时库

解压到你喜欢的目录下，然后把解压后的文件夹移动到 `{你的解压目录}` 目录下，然后把 `{填写你的解压目录}\mingw64\bin` 添加到 `PATH` 环境变量中。

```
curl -OL https://github.com/niXman/mingw-builds-binaries/releases/download/14.2.0-rt_v12-rev0/x86_64-14.2.0-release-win32-seh-ucrt-rt_v12-rev0.7z -o /d/software/develop/```

配置环境变量:
```
D:\software\develop\mingw64\bin
```

**neovim配置目录说明**

windows下的配置目录 `~\AppData\Local\nvim`
windows 下的数据目录 `~\AppData\Local\nvim-data`


#### linux或MacOS迁移配置

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