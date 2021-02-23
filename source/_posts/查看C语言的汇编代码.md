---
title: 查看C语言的汇编代码
date: 2021-01-25 21:01:29
tags:
  - 汇编
categories:
  - 汇编
---

# 查看C语言的汇编代码

## GCC

**GCC, the GNU Compiler Collection**
The GNU Compiler Collection includes front ends for C, C++, Objective-C, Fortran, Ada, Go, and D, as well as libraries for these languages (libstdc++,...). GCC was originally written as the compiler for the GNU operating system. The GNU system was developed to be 100% free software, free in the sense that it respects the user's freedom.


## 选择GCC

> MinGW: 编译目标仅兼容 32 位应用程序，最新的官方二进制版本为 GCC 9.2.0。
> MinGW-w64: 衍生自 MinGW 的项目，编译目标兼容 32 位应用程序与64 位应用程序，最新的官方二进制版本为 GCC 8.1.0。(MingW-W64-builds)
> TDM-GCC: 衍生自 MinGW 和 MinGW-w64 的项目，分为 32 位与 64 位两个版本，32 位版本的编译目标仅兼容 32 位应用程序，64位版本的编译目标兼容 32 位应用程序和 64 位应用程序，最新的官方二进制版本为 GCC 9.2.0。以上三个 GCC 编译器的 Windows 发行版均兼容 POSIX 线程标准 。("pthread.h")个人推荐优先选择 TDM-GCC 作为首选 C / C++ 编译器。

> 作者：砂漠的毒虫
> 链接：https://www.zhihu.com/question/39952667/answer/1133837727
> 来源：知乎


## 安装TDM-GCC

{% asset_img 2021-01-25-21-15-16.png %}

{% asset_img 2021-01-25-21-18-11.png %}

{% asset_img 2021-01-25-21-14-36.png %}


## 生成汇编

{% asset_img 2021-01-26-11-02-40.png %}

{% asset_img 2021-01-26-11-03-11.png %}

{% asset_img 2021-01-26-11-03-28.png %}

```
gcc -og -S main.c
```
这会使GCC运行编译器，产生一个汇编文件main.s,但是不做进一步的工作(通常情况下，它还会继续调用汇编器产生目标代码文件)。

## 展示程序的字节表示

```
gcc -og -c main.c
```
如果我们使用"-c"命令行选项，GCC会编译并汇编该代码,这样就会产生目标代码文件main.o，它是二进制格式的，所以无法直接查看。

要展示程序(main.c)的二进制目标代码，我们用`反汇编器`确定该过程的代码是14字节。然后，在文件main.o上运行GNU调试工具GDB。

要查看机器代码文件的内容，有一类称为`反汇编器(disassembler)`的程序非常有用。

```
objdump -d main.o
```

{% asset_img 2021-01-26-14-08-13.png %}

```
F:\demoCode\syspace\2-ccpp\21-basic\csapp-lab>objdump -d main.o

main.o:     file format pe-i386


Disassembly of section .text:

00000000 <_main>:
   0:   55                      push   %ebp
   1:   89 e5                   mov    %esp,%ebp
   3:   83 ec 08                sub    $0x8,%esp
   6:   83 e4 f0                and    $0xfffffff0,%esp
   9:   b8 00 00 00 00          mov    $0x0,%eax
   e:   83 c0 0f                add    $0xf,%eax
  11:   83 c0 0f                add    $0xf,%eax
  14:   c1 e8 04                shr    $0x4,%eax
  17:   c1 e0 04                shl    $0x4,%eax
  1a:   89 45 fc                mov    %eax,-0x4(%ebp)
  1d:   8b 45 fc                mov    -0x4(%ebp),%eax
  20:   e8 00 00 00 00          call   25 <_main+0x25>
  25:   e8 00 00 00 00          call   2a <_main+0x2a>
  2a:   c7 04 24 00 00 00 00    movl   $0x0,(%esp)
  31:   e8 00 00 00 00          call   36 <_main+0x36>
  36:   b8 00 00 00 00          mov    $0x0,%eax
  3b:   c9                      leave
  3c:   c3                      ret

0000003d <_exchange>:
  3d:   55                      push   %ebp
  3e:   89 e5                   mov    %esp,%ebp
  40:   83 ec 04                sub    $0x4,%esp
  43:   8b 45 08                mov    0x8(%ebp),%eax
  46:   8b 00                   mov    (%eax),%eax
  48:   89 45 fc                mov    %eax,-0x4(%ebp)
  4b:   8b 55 08                mov    0x8(%ebp),%edx
  4e:   8b 45 0c                mov    0xc(%ebp),%eax
  51:   89 02                   mov    %eax,(%edx)
  53:   8b 45 fc                mov    -0x4(%ebp),%eax
  56:   c9                      leave
  57:   c3                      ret

```

## reference
- [GCC, the GNU Compiler Collection](https://gcc.gnu.org/)
- [TDM-GCC 9.2.0 release](https://jmeubank.github.io/tdm-gcc/)
- [mingw-w64](http://www.mingw-w64.org/doku.php)