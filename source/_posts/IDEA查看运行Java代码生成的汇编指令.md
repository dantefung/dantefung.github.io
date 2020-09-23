---
title: IDEA查看运行Java代码生成的汇编指令
date: 2020-09-21 13:31:32
tags:
  - IDEA
  - 汇编
  - JVM
---

# IDEA 查看运行 Java 代码生成的汇编指令

## 方式一: HSDIS

### 一、安装

1. 下载地址: [hsdis-amd64.dll](https://pan.baidu.com/s/1c1GM9Wm4abDTAMA2tv7WeQ),提取码：ssmy
2. 把 hsdis-amd64.dll 放在 \$JAVA_HOME/jre/bin/server 目录下
3. 运行时添加参数:`-XX:+UnlockDiagnosticVMOptions -XX:+PrintAssembly`
   {% asset_img 2020-09-21-14-08-20.png %}

### 二、IDEA 运行添加 VM options

{% asset_img 2020-09-21-15-44-40.png %}

{% asset_img 2020-09-21-15-48-43.png %}

## 方式二: jitwatch

[AdoptOpenJDK/jitwatch](https://github.com/AdoptOpenJDK/jitwatch) 一个用于分析汇编日志的图形界面工具.

### 一、安装

**操作步骤:**

1. 拉取源码
2. 编译源码
3. 启动

**1.拉取源码**

```
git clone https://github.com/AdoptOpenJDK/jitwatch.git
```

**2.编译源码**

```
mvn clean compile test exec:java
```

> NOTE:
> `ant clean compile test run` > `gradlew clean build run`
> 以上两种工具也可! 选择自己顺手的

不出意外的话，你将会看到一个自动弹出的工具界面：

{% asset_img 2020-09-21-16-00-19.png %}

**3.启动**
编译成功后，jitwatch 目录下会生成一些脚本文件，其中`launchUI.bat`就是 windows 平台下的启动脚本.因此，下次启动该工具直接双击该批处理文件即可.

> NOTE:
> `launchUI.sh` 为 linux 平台下的启动脚本.

{% asset_img 2020-09-21-16-05-01.png %}

## 二、使用

**1. 配置源码目录和编译字节码目录**

{% asset_img 2020-09-21-16-41-29.png %}

**2.打开生成的 hotspot.log 文件**

具体 jvm 参数参见附录 1.

{% asset_img 2020-09-21-16-44-02.png %}

{% asset_img 2020-09-21-16-44-23.png %}

{% asset_img 2020-09-21-16-45-50.png %}

{% asset_img 2020-09-21-16-59-40.png %}

## 附录

1. Jvm 参数:[Java Platform, Standard Edition Tools Reference](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/java.html)
   本文涉及到的常用参数:

| 参数                                                    | 解释                                                                                                                                                                    |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -XX:+UnlockDiagnosticVMOptions                          | 解锁用于 JVM 诊断的选项                                                                                                                                                 |
| -XX:+PrintAssembly                                      | 配合反汇编插件（例如 hsdis-amd64.dylib）可以打印出字节码和本地方法的汇编码；必须和 -XX:+UnlockDiagnosticVMOptions 一起使用。                                            |
| -Xcomp                                                  | 在第一次调用时强制编译方法。默认情况下，无论是 -client 模式还是 -server 模式，都需要执行一定次数解释方法的调用才会触发方法的编译。（如果需要 JIT 日志，则不指定该参数） |
| -XX:CompileCommand=compileonly,\*ClassName.methodName： | 只编译类名为 ClassName 中的 methodName 方法，支持使用 \* 作为通配符。可以多次指定 -XX:CompileCommand 添加多条命令。（建议只指定需要的方法，否则将会产生大量的无关日志） |
| -XX:+LogCompilation：                                   | 允许将编译活动记录到当前工作目录中名为 hotspot.log 的文件中。可以通过 -XX:LogFile 指定文件的路径和名字。                                                                |
| -XX:LogFile=path：                                      | 指定日志的路径和文件名。例如：-XX:LogFile=/var/log/hotspot.log                                                                                                          |

**例子:**

```
-XX:+UnlockDiagnosticVMOptions -XX:+PrintAssembly -Xcomp -XX:CompileCommand=compileonly,*VolatileTest.increase -XX:CompileCommand=compileonly,*VolatileTest.decrease -XX:+LogCompilation -XX:LogFile=F:/opt/log/hotspot.log

```

## 参考

[如何优雅的查看 Java 代码的汇编码](https://blog.csdn.net/haihui_yang/article/details/103789384?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~all~first_rank_v2~rank_v25-3-103789384.nonecase&utm_term=ideaui%E6%89%93%E5%8D%B0%E6%B1%87%E7%BC%96)
