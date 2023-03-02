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

## 内置模板

1. `sbc`

**说明:** sbc + 回车

2. 快速生成main方法

`main` + 回车 
`psvm` + 回车

3. 快速生成System.out.println

`sout` + 回车

4. 快速生成TODO注释

`todo` + 回车

5. 快速生成public static final

`psf` + 回车

6. 快速生成public static final String

`psfs` + 回车

同理: 

`psfi`: public static final int
`prsf`: private static fianl 

7. 快速生成获取单例方法

`geti` + 回车

8. 快速生成私有静态final字符串

key + 回车 => 可以快速生成以KEY_为前缀的私有静态final字符串

```

private static final String KEY_光标所在位置= "";

```
9. 快速生成私有final整型变量

`const` + 回车 => 

```

private static final int 光标所在位置 = 958;

```

10. ctrl + j , 调出代码模板生成菜单.


**其他:**

`sb.main` 
`sb.post` 
`sb.get`


## 生成固定代码片段

1. 选中要生成的代码片段

2. 在菜单中选择Tools | Save as Live Template

3. 填写触发单词（Abbreviation）

4. 在代码中输入触发单词后按 Tab，可以重复生成代码片段。


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
|:----:|:----:|
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

----

**Controller Rest Post**
Abbreviation : cpost

Description : Controller层Post请求模板代码 

Variables : 


Template text:
```
@PostMapping("$var1$")
public Result<$var2$> get$var3$() {
	return Result.buildSuccess($var4$);
}

```

----

**lombok**
Abbreviation : lmk

Description : lombok模板代码

Variables : 


Template text:
```
@Data
@AllArgsConstructor
@NoArgsConstructor
@Accessors(chain = true)

```

**CollectionUtils.isEmpty**
Abbreviation : cie

Description : 判断集合是否为空模板

Variables : 


Template text:
```
if (CollectionUtils.isEmpty($var1$)) {
	$var2$
}

```


**StringUtils.isNotBlank**
Abbreviation : sinotb

Description : 判断字符串是否不为空

Variables : 


Template text:
```
if (StringUtils.isNotBlank($var1$)) {
	$var2$
}

```

**StringUtils.isBlank**
Abbreviation : sinotb

Description : 判断字符串是否为空

Variables : 


Template text:
```
if (StringUtils.isBlank($var1$)) {
	$var2$
}

```

**javax bean inject**
Abbreviation : rpr

Description : @Resource方式注入

Variables : 


Template text:
```
@Resource
private $var1$ $var2$;

```

作用范围: Applicable in Java

未完待续... ...


## 巨人肩膀

- [Idea Live Templates代码模板](https://www.cnblogs.com/cxxjohnson/p/8909420.html)
- [idea中live template的详细使用教程](https://www.jianshu.com/p/3974df6572af)
