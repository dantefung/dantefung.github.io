---
title: 从vim迁移到neovim
date: 2022-08-21 00:03:45
tags:
    - vim
---

# 从vim迁移到neovim


<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [从vim迁移到neovim](#从vim迁移到neovim)
    - [为什么我从 vim 迁移到了 neovim](#为什么我从-vim-迁移到了-neovim)
    - [Install](#install)
      - [mac 安装 neovim](#mac-安装-neovim)
      - [ubuntu 安装 neovim](#ubuntu-安装-neovim)
      - [windows 安装 neovim](#windows-安装-neovim)
      - [安装](#安装)
      - [windows迁移配置](#windows迁移配置)
      - [linux或MacOS迁移配置](#linux或macos迁移配置)
      - [推荐几个实用插件](#推荐几个实用插件)
  - [windows下neovim + lazyVim](#windows下neovim--lazyvim)
    - [第一步: 下载](#第一步-下载)
    - [第二步: 配置文件](#第二步-配置文件)
    - [第三步: 启动nvim](#第三步-启动nvim)
    - [第四步: 安装Lazyvim所需的依赖:](#第四步-安装lazyvim所需的依赖)
    - [第五步: 安装Nerd font支持Lazyvim的icon展示](#第五步-安装nerd-font支持lazyvim的icon展示)
  - [扩展阅读(可选)](#扩展阅读可选)

<!-- /code_chunk_output -->



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

**Install from download**

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

> `注意: ` 纯vim迁移过来,只是复用vim的配置,没有额外的需求,其实到这里就可以结束了, 不用再关注本文中Lazyvim部分.
> 以下命令的操作是在git-bash.exe环境下执行的

```
curl -fLo ~/AppData/Local/nvim/autoload/plug.vim  --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

```
cd ~/AppData/Local/nvim/
cp ~/.vimrc ./init.vim
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

## windows下neovim + lazyVim

- [LazyVim for Ambitious Developers](https://lazyvim-ambitious-devs.phillips.codes/course/chapter-1/)

### 第一步: 下载

```
git clone https://github.com/LazyVim/starter.git
```

### 第二步: 配置文件

这个仓库中最重要的就是 `lua 文件夹`和 `init.lua`(这个就是初始化文件) 将其他文件删除

将这两个文件移动到 `~/AppData/Local/nvim/` 目录下

> 

### 第三步: 启动nvim

```
vim
```

{% asset_img 2025-04-03-17-41-21.png %}

> 注意:  这里的vim命令是 `nvim`, 因为用户的配置文件里边改了别名: alias vim=/{你的安装路径}/nvim
>
> Neovim 从 `0.5` 版本开始，已经内置了 **LuaJIT**（高性能 Lua 解释器），可以直接执行 Lua 脚本。这意味着：
> - **无需单独安装 Lua**：Neovim 的安装包已经包含完整的 Lua 环境。
> - **插件和配置直接调用内置 Lua**：所有 Lua 代码（如 LazyVim 的配置文件）都由 Neovim 自身的 Lua 运行时解析。

至此, neovim 已经安装完成, Lazyvim也基本生效了, 但是需要安装一些依赖才能完整使用Lazyvim.


### 第四步: 安装Lazyvim所需的依赖:

```
neovide git lazygit gcc ripgrep fd unzip tree-sitter luarocks
```
- tree-sitter - 用于代码语法解析
- fzf - 命令行模糊查找工具
- fd - 现代化替代 find 的工具
- luarocks - Lua 包管理器
- ripgrep - 快速文本搜索 (替代 grep)
- unzip - 解压 ZIP 文件
- lazygit - Git 命令行界面管理工具
- gcc - GNU C/C++ 编译器
- git - 版本控制管理工具
- neovide - Neovim 的图形界面

>
> 以上的需要安装的依赖, 如果你的机器上没有安装, 请先安装.
> 这些软件包都在github上有对应的安装包, 可以直接下载安装.
> 一般都是编译好的二进制文件, windows平台都是exe文件, 配置好对应的环境变量即可.
> 但是, 手动安装的过程太过繁琐, 所以下面使用包管理工具安装依赖(对如何手动安装依赖, 可以移步到下方的扩展阅读或自行百度).
> 

**windows下包管理工具的选择**

*Chocolatey*

`Chocolatey` 的社区仓库里有 9000 多个软件，几乎涵盖了所有的主流软件。Chocolatey 还提供一个 GUI 程序，方便普通用户使用。

由于 Chocolatey 采用常规全局模式安装软件，所以需要管理员权限。如果采用非管理员模式，Chocolatey 只能安装 Portable 版本的软件，可用软件数量就少了很多。

*Scoop*

`Scoop` 可以添加第三方的 bucket，也就是安装源。默认的 main bucket 里有 900 多个软件，extras bucket 里有 1300 多个软件。值得一提的是，还有个 versions bucket，里面包含了一些软件的历史版本。虽然 Scoop 上的软件数量不多，但是涵盖了大多数开发工具。对于软件开发人员来说，使用 Scoop 来搭建开发环境相当方便。

与 Chocolatey 采用系统全局安装方式不同的是，Scoop 安装的软件都保存在 %userprofile%\scoop\apps 路径下，不会污染全局环境，不需要管理员权限就可以安装软件。

**安装Scoop**

> 这里以下写好的安装脚本, 执行环境需要是管理员权限

*0: 代理环境准备(如果你不是在墙内,可以跳过)*


***powershell代理***

以管理员的身份运行powershell:

```
Start-Process powershell -Verb runAs
```

设置代理

```
netsh winhttp set proxy "127.0.0.1:7890"
```

检查代理:

```
 netsh winhttp show proxy
```
测试连接:

```
iwr -Uri https://www.google.com

# or

iwr -Uri https://www.google.com | Select-Object -Property StatusCode
```

关闭proxy:

```
netsh winhttp reset proxy
```



*1. 创建文件 `install.ps1`*

内容如下:

```powershell
# Self-elevate the script if not running as administrator
if (-not ([Security.Principal.WindowsPrincipal] [Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole(`
    [Security.Principal.WindowsBuiltInRole] "Administrator")) {
    Write-Host "Restarting as administrator..."
    Start-Process powershell "-NoProfile -ExecutionPolicy Bypass -File `"$PSCommandPath`"" -Verb RunAs
    exit
}


# Set execution policy
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser -Force

# Install Scoop
# Check if Scoop is already installed
$ScoopInstalled = Get-Command scoop -ErrorAction SilentlyContinue

if (-not $ScoopInstalled) {
    Write-Host "Scoop not found. Installing Scoop..."
    irm get.scoop.sh -outfile 'install.ps1'
    iex "& {$(irm get.scoop.sh)} -RunAsAdmin"

    # Clean up install.ps1
    if (Test-Path "./install.ps1") {
        Remove-Item "./install.ps1" -Force
        Write-Host "Removed install.ps1"
    }
} else {
    Write-Host "Scoop is already installed. Skipping installation."
}

# Define Scoop directories
$SCOOP_USER = 'D:\software\develop\Scoop\userApps'
$SCOOP_GLOBAL = 'D:\software\develop\Scoop\globalApps'

# Create directories if not exist
if (!(Test-Path $SCOOP_USER)) {
    New-Item -ItemType Directory -Path $SCOOP_USER -Force | Out-Null
    Write-Host "Created user app directory: $SCOOP_USER"
} else {
    Write-Host "User app directory already exists: $SCOOP_USER"
}

if (!(Test-Path $SCOOP_GLOBAL)) {
    New-Item -ItemType Directory -Path $SCOOP_GLOBAL -Force | Out-Null
    Write-Host "Created global app directory: $SCOOP_GLOBAL"
} else {
    Write-Host "Global app directory already exists: $SCOOP_GLOBAL"
}

# Set environment variables for current user
[Environment]::SetEnvironmentVariable('SCOOP', $SCOOP_USER, [EnvironmentVariableTarget]::User)
[Environment]::SetEnvironmentVariable('SCOOP_GLOBAL', $SCOOP_GLOBAL, [EnvironmentVariableTarget]::User)

Write-Host "Environment variables set:"
Write-Host "  SCOOP = $SCOOP_USER"
Write-Host "  SCOOP_GLOBAL = $SCOOP_GLOBAL"

# Set them for current session as well
$env:SCOOP = $SCOOP_USER
$env:SCOOP_GLOBAL = $SCOOP_GLOBAL

# Install aria2 to speed up downloads
scoop install aria2

# Clean up install.ps1
if (Test-Path "./install.ps1") {
    Remove-Item "./install.ps1" -Force
    Write-Host "Removed install.ps1"
}

Write-Host "Setup complete"

```
*2. 管理员运行powershell*

执行如下命令:

```
./install.ps1
```

**Scoop安装LazyVim所需依赖**


打开git-bash.exe， 新建文件 `scoop_tools_installer.sh`, 写入如下内容

```bash   

#!/usr/bin/env bash

# 定义输出颜色（绿色、黄色、重置）
GREEN="\033[32m"
YELLOW="\033[33m"
RESET="\033[0m"

# 工具名到描述的映射表（用于展示）
declare -A TOOL_MAP=(
  [httpd]="Apache HTTP Server (含 ab 性能测试工具)"
  [fzf]="命令行模糊查找工具"
  [fd]="现代化替代 find 的工具"
  [gcc]="GNU C/C++ 编译器"
  [ripgrep]="快速文本搜索 (替代 grep)"
  [unzip]="解压 ZIP 文件"
  [tree-sitter]="用于代码语法解析"
  [luarocks]="Lua 包管理器"
  [lazygit]="Git 命令行界面管理工具"
  [jq]="轻量级 JSON 处理工具"
  [mycli]="MySQL 命令行客户端"
  [shellcheck]="Shell 脚本静态分析工具"
  [htop]="交互式进程查看器"
  [axel]="多线程下载工具"
  [sz]="Zmodem 传输工具"
  [rz]="Zmodem 传输工具"
  [cloc]="代码统计工具"
  [tmux]="终端复用工具"
  [you-get]="媒体下载工具"
  [thefuck]="命令行命令更正工具"
  [wget]="下载文件工具"
)

# 工具名到使用提示的映射（安装后展示）
declare -A TOOL_TIPS=(
  [httpd]="运行 ab 性能测试：ab -n 100 -c 10 http://localhost/"
  [fzf]="使用：git ls-files | fzf"
  [fd]="使用：fd keyword"
  [gcc]="使用：gcc hello.c -o hello"
  [ripgrep]="使用：rg 'pattern' ./src"
  [unzip]="使用：unzip file.zip"
  [tree-sitter]="使用需搭配 Neovim / CLI 工具"
  [luarocks]="使用：luarocks install <package>"
  [lazygit]="使用：lazygit"
  [jq]="使用：echo '{"name": "John"}' | jq .name"
  [mycli]="使用：mycli -u root -p"
  [shellcheck]="使用：shellcheck your_script.sh"
  [htop]="使用：htop"
  [axel]="使用：axel https://example.com/file.zip"
  [sz]="使用：sz filename"
  [rz]="使用：rz"
  [cloc]="使用：cloc ."
  [tmux]="使用：tmux new-session -s mysession"
  [you-get]="使用：you-get https://www.youtube.com/watch?v=example"
  [thefuck]="使用：fuck"
  [wget]="使用：wget --help"
)

selected_tools=()
TOOL_KEYS=()

# 检查 scoop 是否安装，若无则使用管理员权限安装
function check_scoop() {
  if ! command -v scoop &>/dev/null; then
    echo -e "${YELLOW}未检测到 scoop，正在以管理员权限安装...${RESET}"
    powershell -Command "Write-Host 'Scoop not found. Installing Scoop...'; irm get.scoop.sh -outfile 'install.ps1'; iex \"& {$(irm get.scoop.sh)} -RunAsAdmin\""
  else
    echo -e "${GREEN}✔ Scoop 已安装${RESET}"
  fi
}

# 添加常用的 bucket（仓库），以获取更多软件
function add_buckets() {
  echo -e "${YELLOW}添加常用 bucket...${RESET}"
  scoop bucket add main || true      # 主仓库
  scoop bucket add extras || true    # 额外工具仓库
  scoop bucket add versions || true  # 版本控制类工具仓库
}

# 交互式选择安装的工具
function select_tools_interactive() {
  echo -e "${YELLOW}请选择你要安装的工具（空格分隔编号，如 1 3 5）：${RESET}"
  
  i=1
  # 遍历 TOOL_MAP 中的所有键（工具名）
  for tool in "${!TOOL_MAP[@]}"; do
    echo "  [$i] ${tool} - ${TOOL_MAP[$tool]}"
    TOOL_KEYS[$i]=$tool
    ((i++))  # 递增编号
  done

  read -rp "你的选择: " selection
  for index in $selection; do
    selected_tools+=("${TOOL_KEYS[$index]}")
  done
}

# 安装选择的工具
function install_tools() {
  for tool in "${selected_tools[@]}"; do
    echo -e "${YELLOW}安装 ${tool}...${RESET}"
    scoop install "$tool"
  done
  echo -e "\n${GREEN}✅ 所有工具已安装完成！${RESET}"
}

# 显示每个工具的使用小贴士
function show_tips() {
  echo -e "\n${YELLOW}🧠 使用小贴士:${RESET}"
  for tool in "${selected_tools[@]}"; do
    echo -e "${GREEN}- $tool:${RESET} ${TOOL_TIPS[$tool]}"
  done
}

# 解析命令行参数
function parse_args() {
  while [[ $# -gt 0 ]]; do
    case "$1" in
      --all)
        # --all: 安装所有工具
        selected_tools=("${!TOOL_MAP[@]}")
        return
        ;;
      --tools)
        # --tools 后面接多个工具名
        shift
        while [[ $# -gt 0 && ! $1 =~ ^-- ]]; do
          selected_tools+=("$1")
          shift
        done
        ;;
      *)
        echo -e "${YELLOW}⚠️ 未知参数：$1${RESET}"
        shift
        ;;
    esac
  done
}

# 主函数
function main() {
  check_scoop       # 检查 Scoop 安装
  add_buckets       # 添加 bucket

  if [[ $# -gt 0 ]]; then
    parse_args "$@"  # 解析命令行参数
  fi

  # 如果没有通过参数选择工具，就进入交互模式
  if [[ ${#selected_tools[@]} -eq 0 ]]; then
    select_tools_interactive
  fi

  install_tools
  show_tips
}

# 执行主函数，传入所有参数
main "$@"


```

执行安装

```
./scoop_tools_installer.sh --all
```

**进入neovim**

> 进入neovim后, lazyvim会自动安装依赖包

```
nvim
```

{% asset_img 2025-04-03-17-41-21.png %}

### 第五步: 安装Nerd font支持Lazyvim的icon展示


这是关键的一步，因为Neovim显示的许多细节依赖于额外的“字形”才能正确显示。你问，什么是字形？简单来说：单词中的一个字符在技术上是一个字形，图标也是一个字形，边框字符或角落字符等也是字形。Neovim使用这些字形一点一点构建用户界面。你可以在Nerdfont.com找到大量可免费下载的Nerd字体。我个人喜欢Caskaydia Cove，所以我下载了它。

然后通过右键点击你刚刚下载的文件夹并选择“全部解压缩”，来解压（提取）文件夹，然后打开弹出的新解压后的文件夹。~你会看到一大堆文件，你需要找到那些标明与Windows兼容的文件。~ 编辑提示 - 此字体现在似乎已经更新，所以只需全部选中即可，但请注意，此下载包含Monospace（等宽）和Regular（常规）版本，因此你会得到这两者。


{% asset_img 2025-04-16-23-03-40.png %}


然后，右键点击你选中的字体组，并选择“为所有用户安装”。这将使这些字体在你的系统中对所有用户都可用。

{% asset_img 2025-04-17-00-14-54.png %}

{% asset_img 2025-04-17-00-12-57.png %}

{% asset_img 2025-04-17-00-09-36.png %}

如图搜所示, icon都显示正常了:

{% asset_img 2025-04-17-00-17-52.png %}


## 扩展阅读(可选)

有时候, 一些包管理工具没有对应的软件源, 则需要手动安装了对应的软件包.

**手动安装gcc环境**

Linux 和 Mac OS 这里应该不会有问题。

对于 Windows，因为 `Treesitter` 需要 C 语言编译器编译一些语法解析器，所以我们需要装一个 C 编译器.

[x86_64-14.2.0-release-win32-seh-ucrt-rt_v12-rev0.7z](https://github.com/niXman/mingw-builds-binaries/releases/download/14.2.0-rt_v12-rev0/x86_64-14.2.0-release-win32-seh-ucrt-rt_v12-rev0.7z)，直接点击就可以下载到浏览器下载目录下。如果有其他需要，也可以下载其他的压缩包，其中：

- `i686` 是 32 位版本，x86_64 是 64 位版本

- `win32` 适用于 windows 操作系统，posix 适用于其他操作系统

- `dwarf` 是 32 位的异常处理系统，seh 适用于 64 位

- `msvcrt` 是旧版微软 C 运行时库，ucrt 是新版运行时库

解压到你喜欢的目录下，然后把解压后的文件夹移动到 `{你的解压目录}` 目录下，然后把 `{填写你的解压目录}\mingw64\bin` 添加到 `PATH` 环境变量中。

```
curl -OL https://github.com/niXman/mingw-builds-binaries/releases/download/14.2.0-rt_v12-rev0/x86_64-14.2.0-release-win32-seh-ucrt-rt_v12-rev0.7z -o /d/software/develop/
```

配置环境变量:
```
D:\software\develop\mingw64\bin
```

**手动安装chocolatey**

管理员身份打开 PowerShell 的命令行界面，输入如下命令：

```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

```

```
choco config set location D:/software/develop/
```

>
> **1. Chocolatey 的默认安装路径是什么？**\
> A: `C:\ProgramData\chocolatey\lib`。
> 

如果安装过内容, C:\ProgramData\chocolatey\lib中的内容如下:
```
-rw-r--r-- 1 fhlin 197609   21 Apr  9 23:46 _processed.txt
-rwxr-xr-x 1 fhlin 197609 384K Apr 10 00:41 bin2c5.1.exe*
-rwxr-xr-x 1 fhlin 197609 144K Feb 27 14:04 choco.exe*
-rwxr-xr-x 1 fhlin 197609 384K Apr 10 00:39 fd.exe*
-rwxr-xr-x 1 fhlin 197609 384K Apr 10 00:40 fzf.exe*
-rwxr-xr-x 1 fhlin 197609 383K Apr 10 00:41 lua5.1.exe*
-rwxr-xr-x 1 fhlin 197609 384K Apr 10 00:41 luac5.1.exe*
-rwxr-xr-x 1 fhlin 197609 384K Apr 10 00:41 luarocks.exe*
-rwxr-xr-x 1 fhlin 197609 384K Apr 10 00:41 luarocks-admin.exe*
-rwxr-xr-x 1 fhlin 197609 384K Apr 10 00:41 luarocksw.exe*
-rw-r--r-- 1 fhlin 197609 4.4K Feb 27 14:04 RefreshEnv.cmd
-rwxr-xr-x 1 fhlin 197609 384K Apr 10 00:38 rg.exe*
-rwxr-xr-x 1 fhlin 197609 383K Apr 10 00:41 wlua5.1.exe*
```

> **2. 如何更改 Chocolatey 的安装路径？**\
> A: 使用 `choco config set location <new-path>` 命令。
> 
> **3. 如何查找已安装软件包的路径？**\
> A: 使用 `choco list -lo` PowerShell 命令或 ChocolateyGUI。
> 
> **4. 更改 Chocolatey 安装路径后需要做什么？**\
> A: 重新启动 PowerShell 以使更改生效。

 

```
choco install neovide ripgrep fd fzf luarocks    
```
> 如果你很多都没装, 则下边的命令会全一些
> scoop install neovim neovide git lazygit gcc ripgrep fd unzip tree-sitter luarocks
