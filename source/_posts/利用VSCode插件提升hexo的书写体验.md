---
title: 利用VSCode插件提升hexo的书写体验
date: 2020-08-27 23:53:20
tags:
---

# 利用VSCode插件提升hexo体验

Hexo的编写与部署可以通过vscode进行，同时通过对插件的使用也可以增强体验，提升效率

1\. 图片剪切插件 [Paste Image](https://marketplace.visualstudio.com/items?itemName=mushan.vscode-paste-image)


hexo3.0版本中可以在_config.yml中设置

```
post_asset_folder: true
```

这样在hexo new 的时候会生成同名的文件夹，同时在md文件中输入

```
{% asset_img xxx %}
```

就可以插入这个文件夹中的图片。为了方便进行这个步骤，使用vscode中的插件Paste Image，安装之后，在user-settings里新增两条配置：


~~"pasteImage.path": "${currentFileNameWithoutExt}/",~~ 

~~"pasteImage.insertPattern": "{% asset_img ${imageFilePath} %}"~~

更正为:
```
"pasteImage.path": "${currentFileNameWithoutExt}",
"pasteImage.insertPattern": "{% asset_img ${imageFileName} %}"
```
操作步骤图示:

<center>{% asset_img 2020-08-28-01-51-34.png %}</center><br/>


<center>{% asset_img 2020-08-28-01-55-04.png %}</center><br/>


<center>{% asset_img 2020-08-28-01-55-52.png %}</center><br/>


<center>{% asset_img 2020-08-28-01-56-21.png %}</center><br/>


这样粘贴图片的时候会自动保存到文件夹中，并自动引用。注意这个插件只适合与剪切板使用，直接拖拽文件是不管用的。

2\. markdown预览插件 [Markdown Preview Enhanced](https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

利用该插件预览

```
{% asset_img xxx %}
```

形式插入的图片，主要分为两步

##### 1. ctrl+shift+P 找到 Markdown Preview Enhanced: Extend Parser，打开parser.js"

##### 2. 修改其中onWillParseMarkdown方法为：

```
onWillParseMarkdown: function(markdown) {
  return new Promise((resolve, reject)=> {
    markdown = markdown.replace(
      /\{%\s*asset_img\s*(.*)\s*%\}/g,
      (whole, content) => (`![](${content})`)
    )
    return resolve(markdown)
  })
},
```
