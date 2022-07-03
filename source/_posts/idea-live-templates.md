---
title: idea live templates 
date: 2022-04-12 09:31:56
tags:
 - IDEA
categories:
 - 工程效率
---

# IDEA live template

<center>
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=65778&auto=1&height=66"></iframe></center>

## 目的

创建代码模板进行快速代码编写，如sout-->System.out.println();

## 设置

**1、定义模板**

{% asset_img 2022-04-12-09-38-30.png %}


Abbreviation：模板的缩略名称，如sout;

Template text：模板的代码片段，可以用自定义变量。

**2、自定义变量:**

这里我们自定义了CLASS变量（使用时需要前后都加上$）,但Idea并不识别，这就需要对自定义的变量进行编辑，Edit Variables。

{% asset_img 2022-04-12-09-39-50.png %}

Name:我们的自定义变量名称；

Expression:变量替换表达式，这里我们使用了Idea模板支持的内置函数className()表示类名，更多的内置函数参考：Creating and Editing Template Variables

Default value：表达式计算失败时的默认值。

**3、设置应用范围**

{% asset_img 2022-04-12-09-41-33.png %}


{% asset_img 2022-04-12-09-41-58.png %}


## 常用模板

**loggerDefineStatic**

```
private static final Logger logger = LoggerFactory.getLogger($CLASS_NAME$.class);
```

CLASS_NAME  className()

**loggerDefineThis**

```
private final Logger logger = LoggerFactory.getLogger(this.getClass());
```

**logStart**

```
logger.info("op=start_$METHOD_NAME$, $PARAMS_FORMAT$", $PARAMS$);
```

自定义变量:
|变量名|取值|
|---|---|
|METHOD_NAME|methodName()|
|PARAMS_FORMAT|groovyScript("_1.collect{it+'={}'}.join(',')", methodParameters())|
|PARAMS|groovyScript("_1.collect{it}.join(',')", methodParameters())|

**test**

```
@Test
public void test() {
    $END$
}
```

# Live-Templates
分享个人 IntelliJ idea Live Template 配置,希望可以帮助大家提高编程效率,如果你那也有一些好用的template欢迎添加.

对于Live Template 还熟悉的童鞋可以先查看[**本文**](http://blog.xiaohansong.com/2017/03/17/idea-live-templates/).
## 实体类相关

Abbreviation : pi

Description : private Integer

Template text:
```
/**
 * $var1$
 */
private Integer $end$;

```
****
Abbreviation : pl

Description : private list

Template text:
```
/**
 * $var1$
 */
private List<$var2$> $end$;

```
****
Abbreviation : pm

Description : private map

Template text:
```
/**
 * $var1$
 */
private Map<$var2$, $var3$> $end$;

```
****
Abbreviation : ps

Description : private String

Template text:
```
/**
 * $var1$
 */
private String $end$;

```
****
Abbreviation : pss

Description : private Set

Template text:
```
/**
 * $var1$
 */
private Set<$var2$> $end$;

```
****
## 通用

Abbreviation : list

Description : list

作用域 : Java.Statement(其他的不用选)

Template text:
```
List<$var1$> list = new ArrayList<>();

```
****
Abbreviation : map

Description : map

作用域 : Java.Statement(其他的不用选)

Template text:
```
Map<$var1$, $var2$> map = new HashMap<>();

```
****

Abbreviation : set

Description : set

作用域 : Java.Statement(其他的不用选)

Template text:
```
Set<$var1$> set = new HashSet<>();

```

## log相关

Abbreviation : param

Description : log info method params

Variables : 

+ end = methodName()
+ params = groovyScript("'\"' + _1.collect { it + ' = [\" + ' + it + ' + \"]'}.join(', ') + '\"'", methodParameters())

Template text:
```
log.info("$end$ :" + $params$);

```

{% asset_img 2022-04-12-10-04-28.png %}

 ***

**Spring bean注入**

Abbreviation : apr

Description : spring bean inject 

Variables : 


Template text:
```
@Autowired
private $var1$ $var2$;

```

未完待续... ...


## 巨人肩膀

- [Idea Live Templates代码模板](https://www.cnblogs.com/cxxjohnson/p/8909420.html)
- [idea中live template的详细使用教程](https://www.jianshu.com/p/3974df6572af)
