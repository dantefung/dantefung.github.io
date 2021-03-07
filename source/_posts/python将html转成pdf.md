---
title: python将html转成pdf
date: 2021-03-07 22:54:16
tags:
 - python
categories:
 - python
---

# python将html转成pdf

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.cnblogs.com](https://www.cnblogs.com/taceywong/p/5643978.html)

----

@Modifer: DANTE FUNG
@Date: 2021-03-08 00:00:58

----

主要使用的是 wkhtmltopdf 的 Python 封装——pdfkit

安装
--

**1. Install python-pdfkit**:

```
$ pip install pdfkit
```

**2. Install wkhtmltopdf**:

*   Debian/Ubuntu:

```
$ sudo apt-get install wkhtmltopdf
```

*   Redhat/CentOS

```
sudo yum intsall wkhtmltopdf
```

*   MacOS

```
brew install Caskroom/cask/wkhtmltopdf
```
> Note: 这种安装方式比较慢.

直接去官网下载安装包安装即可。
 Download the disk image from [wkhtmltopdf downloads list](http://wkhtmltopdf.org/downloads.html) and copy the file to a directory where PDFKit can find it. Homebrew is also available, run brew install Caskroom/cask/wkhtmltopdf.

{% asset_img 2021-03-07-23-59-07.png %}

使用
--

一个简单的例子:

```
import pdfkit

	pdfkit.from_url('http://google.com', 'out.pdf')
	pdfkit.from_file('test.html', 'out.pdf')
	pdfkit.from_string('Hello!', 'out.pdf')
```

你也可以传递一个 url 或者文件名列表:

```
pdfkit.from_url(['google.com', 'yandex.ru', 'engadget.com'], 'out.pdf')
	pdfkit.from_file(['file1.html', 'file2.html'], 'out.pdf')
```

也可以传递一个打开的文件:

```
with open('file.html') as f:
        pdfkit.from_file(f, 'out.pdf')
```

如果你想对生成的 PDF 作进一步处理， 你可以将其读取到一个变量中:

```
# 设置输出文件为 False，将结果赋给一个变量
    pdf = pdfkit.from_url('http://google.com', False)
```

你可以制定所有的 wkhtmltopdf `选项 <http://wkhtmltopdf.org/usage/wkhtmltopdf.txt>`. 你可以移除选项名字前面的 '--' . 如果选项没有值, 使用 `None, False`or `*` 作为字典值:

```
options = {
	    'page-size': 'Letter',
	    'margin-top': '0.75in',
	    'margin-right': '0.75in',
	    'margin-bottom': '0.75in',
	    'margin-left': '0.75in',
	    'encoding': "UTF-8",
	    'no-outline': None
	}

	pdfkit.from_url('http://google.com', 'out.pdf', options=options)
```

默认情况下, PDFKit 将会显示所有的 `wkhtmltopdf` 输出. 如果你不想看到这些信息，你需要传递一个 `quiet` 选项:

```
options = {
        'quiet': ''
        }

    pdfkit.from_url('google.com', 'out.pdf', options=options)
```

由于 wkhtmltopdf 的命令语法 , **TOC** 和 **Cover** 选项必须分开指定:

```
toc = {
	    'xsl-style-sheet': 'toc.xsl'
	}

	cover = 'cover.html'

	pdfkit.from_file('file.html', options=options, toc=toc, cover=cover)
```

当你转换文件、或字符串的时候，你可以通过 _css_ 选项指定扩展的 CSS 文件。

```
# 单个 CSS 文件
    css = 'example.css'
    pdfkit.from_file('file.html', options=options, css=css)

    # Multiple CSS files
    css = ['example.css', 'example2.css']
    pdfkit.from_file('file.html', options=options, css=css)
```

你也可以通过你的 HTML 中的 meta tags 传递任意选项：

```
body = """
	    <html>
	      <head>
	        <meta />
	        <meta />
	      </head>
	      Hello World!
	      </html>
	    """

	pdfkit.from_string(body, 'out.pdf') #with --page-size=Legal and --orientation=Landscape
```

配置
--

每个 API 调用都有一个可选的参数。这应该是 `pdfkit.configuration()`API 调用的一个实例. 采用 configuration 选项作为初始化参数。可用的选项有:

*   `wkhtmltopdf` ——`wkhtmltopdf` 二进制文件所在的位置。默认情况下 `pdfkit` 会尝试使用 `which` (在类 UNIX 系统中) 或 `where` (在 Windows 系统中) 来判断.
*   `meta_tag_prefix` -- `pdfkit` 的前缀指定 meta tags（元标签） - 默认情况是 `pdfkit-`

示例 ：针对 `wkhtmltopdf` 不在系统路径中（不在`$PATH` 里面）:

```
config = pdfkit.configuration(wkhtmltopdf='/opt/bin/wkhtmltopdf'))
    pdfkit.from_string(html_string, output_file, configuration=config)
```

问题
--

*   `IOError: 'No wkhtmltopdf executable found'`:

确保 wkhtmltopdf 在你的系统路径中（`$PATH`）， 会通过 configuration 进行了配置 (详情看上文描述)。 在 Windows 系统中使用 `where wkhtmltopdf` 命令 或 在 linux 系统中使用 `which wkhtmltopdf` 会返回 wkhtmltopdf 二进制可执行文件所在的确切位置.

*   `IOError: 'Command Failed'`
    
    如果出现这个错误意味着 PDFKit 不能处理一个输入。你可以尝试直接在错误信息后面直接运行一个命令来查看是什么导致了这个错误 （某些版本的 wkhtmltopdf 会因为段错误导致处理失败）
    
*   正常生成，但是出现中文乱码
    

确保两项：  
1）、你的系统中有中文字体  
2）、在 html 中加入 **<meta charset="UTF-8">**

下面是我随便写的一个 HTML 表格：

```
<html>
<head><meta charset="UTF-8"></head>
<body>
<table width="400" border="1">
 <tr>
  <th align="left">Item....</th>
  <th align="right">1</th>
 </tr>
 <tr>
  <td align="left">衣服</td>
  <td align="right">$241.10</td>
 </tr>
 <tr>
  <td align="left">化妆品</td>
  <td align="right">$30.00</td>
 </tr>
 <tr>
  <td align="left">食物</td>
  <td align="right">$730.40</td>
 </tr>
 <tr>
  <th align="left">tOTAL</th>
  <th align="right">$1001.50</th>
 </tr>
</table>
</body>
</html>
```

下面是生成的 PDF 截图  
{% asset_img 673170-20160705155800186-1254560177.png %}
