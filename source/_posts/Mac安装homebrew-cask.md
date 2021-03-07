---
title: Mac安装homebrew-cask
date: 2021-03-07 23:27:18
tags:
 - mac
categories:
 - mac
---


# Mac安装homebrew-cask

## 安装方式
### 方式一:手动clone

1.进入homebrew目录：
```
cd "$(brew --repo)/Library/Taps/homebrew/"
```
2.开始clone
```
git clone git://mirrors.ustc.edu.cn/homebrew-cask.git /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask
```

### 方法二：利用官网文件安装

从官网上下载homebrew-cask-master.zip压缩包，解压后将文件夹名改为homebrew-cask
然后将其拷贝放入/usr/local/Homebrew/Library/Taps/homebrew中，与homebrew-core文件夹同级
至此完成homebrew-cask安装

### 方法三：使用以下命令进行安装
```
brew install cask
```

## 换源
```
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-cask"
git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git
```

```
cd
brew update
```

```
# 运行HomeBrew医生，查看警告文件
brew doctor
```

## Reference

- [Mac快速安装HomeBrew和HomeBrew-Cask方法](https://blog.csdn.net/m0_46197393/article/details/106752367)


