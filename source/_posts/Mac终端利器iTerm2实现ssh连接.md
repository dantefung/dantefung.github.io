---
title:  Mac终端利器iTerm2实现ssh连接
author: dantefung
tags:
  - 利器
  - Item2
  - ssh
categories:
  - 利器
  - mac
date: 2020-02-29 00:44:34
---


## Mac终端利器iTerm2实现ssh连接

### 目标

通过Mac OS x 系统下的终端利器Item2实现ssh远程连接

### 实现步骤

#### 第一步: 新建shell文件

> 笔者偏好讲shell文件放置在/Users/admin/Documents/ssh目录下，读者可以根据自己的喜好自行放置

首先在/Users/admin/Documents/ssh目录下新建一个以.sh为后缀的文件，文件名根据个人喜好命名，假定笔者将其命名为server.sh

> Note: 现在你的ssh文件的目录为/Users/admin/Documents/ssh/server.sh

#### 第二步: 填写脚本代码

打开server.sh文件，并填写如下内容:

```shell
#!/usr/bin/expect -f  
  set port 22  
  set user dante
  set host 这里填写你要连接的服务器的IP
  set password 这里填写你要连接的服务器的登录密码
  set timeout -1
   
  spawn ssh -p$port $user@$host  
  expect "*assword:*"  
  send "$password\r"  
  interact  
  expect eof

```

> Note: 本文不针对脚本中的代码进行解释说明，感兴趣的读者可自行通过搜索引擎寻求答案。

#### 第三步： 配置iTerm2的profile

假定你已经安装好了iTerm2了，现在请你打开iTem2终端。

此时，你会看到屏幕顶端的菜单栏：

`iTerm2`  `Shell` `Edit` `View` `Session` `Profiles` `Toolbelt` `Windows` `Help`

![image-20200229013028044](/images/image-20200229013028044.png)

[x] 选择`iTem2`-->`Preferences...`,进入偏好设置页面，不出意外的话，你将见到菜单栏:

`General` `Appearance` `Profiles` `Key` `Arrangements` `Pointer` `Advanced`


[x] 选择`Profiles`，新增一个profile，命名为server，Command选择`Custom Shell`，配置路径为`/Users/admin/Documents/ssh/server.sh`，然后关闭窗口即可。

![image-20200229011920187](/images/image-20200229011920187.png)

#### 第四步：在终端主页面选择profiles菜单下的server

> Note: 一般来说，你需要先手动通过`ssh -p$port $user@$host  `命令先手动连接一次。然后在通过选择iTerm2终端菜单栏Profiles里边配置的`服务器连接`

![image-20200229013441719](/images/image-20200229013441719.png)