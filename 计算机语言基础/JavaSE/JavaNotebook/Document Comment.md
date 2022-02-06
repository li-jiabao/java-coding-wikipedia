---
title: Document Comment
author: lijiabao
date: 2020-11-28 12:57:50.343809500 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
#### javadoc
javadoc是Java中一个很有用的工具，可以从源文件生成HTML你的文献，比如Online API documentation就是javadoc从标准Java库的源文件生成的。
换到包含源文件的目录下，如com.horstman.corejava，你需要定位到包含com子目录的目录下（这个目录包含有overview.html文件）然后就可以执行命令来运行javadoc。需要生成documentation的源代码的包名需要对应上才可以，否则可能会出错
- `javadoc -d destDirectory nameOfPackage`
- `javadoc -d destDirectory nameOfPackage1 nameOfPackage2 ..`可以生成多个包源文件的documentation到指定目录
- `javadoc -d destDirectory source.java`可以生成default包下的源文件的documentation
如果省略-d选项，HTML文件生成在当前目录下
详细的使用看官方javadoc的使用介绍
- `javadoc -link standardclassHyperlink`举个例子：`javadoc -link http://docs.oracle.com/javase/doc/api *.java`这就将标准库连接到你的documentation中来了
- `javadoc -linksource`将原文件转变为html的代码文件，没有颜色的区别对比，纯文本代码，拥有行数字

javadoc生成文献使用的注释是/\*\*......\*/。正是由于javadoc的存在，让你的文档和代码放在了一起，这样可以随时更新文档 很方便。如果放在不同文件中，可能文件分离后很难找到，同时更新不方便。

#### 可以插入注释的位置
javadoc可以提取以下几个项目中的信息：
- Package
- public class and interface
- public and protected fields
- public and protected constructor and methods

注释文档块中还可以使用tag@，@开头的有annotation还有一些特定的内容，如：@author、@param、@depreciated等等，还支持HTML标签如：<img>、<code></code>、<em></em>、<strong></strong>，最好不适用heading标签，可能会扰乱生成的HTML。
> 如果注释包含其他文件中的链接，最好将他们放在包含源文件的目录下的子目录`doc-files`中，javadoc会自动复制这个目录并将其放在生成的documentation目录下


#### class comments
class的注释必须放在import语句后class语句前。
```
/**
 *this is a example of class comment
 *<code>ClassName</code> is used to do some useful things.
 *every line don't have '*' is also ok
 @author Lijiabao
*/
```


#### method comment
每一个方法注释都必须紧跟着方法的描述，除了可以使用正常的tag和文本之外，还可以使用以下几个标签：
- `@return description`：
这个标签是用来增加return返回值这一项描述的，这个描述可以跨越多行并且可以使用HTML标签
- `@param variable description`：
这个标签使用来增加parameter参数选项的，可以使用HTML标签，可跨行。所有的parameter标签需要和方法对应
- `@throws class description`：
增加的是方法可抛出的异常的描述


#### filed comments
对属性，一般只需要document公共属性，一般说来也就是常量了。
```
/**
 * this is a constant used for describing PI
*/
public static final PI = 3.1415926;
```


#### general comments
以下几个标签是用来描述class的：
- `@author name`
这个标签是用来声明作者的，`@author lijiabao`
- `@version text`
用来描述版本信息的，text可以是对版本的描述

以下几个用在所有的documentation注释中的
- `@since text`
用来描述哪个版本开始使用这个功能的，`@since version 1.7.2`
- `@deprecated text`
这个标签用来告知某个item不再开始使用，text应该表明用来替换的内容，`@deprecated Use <code>setVisible(true)</code> instead`

###### 文档中引用超链接
- `@see reference`
这个标签再see also部分添加一个超链接，可以在类和方法中使用，下面显示了reference的样式
```
package.class#feature label
<a href="...">label</a>
"text"
```
第一种情况很有用，你提供类】方法或者变量的名字，javadoc插入一个超链接到你的documentation
`@see com.horstman.corejava.Employee#raiseSalary(double)`
上面的例子将超链接给到com.horstman.corejava包下的raiseSalary(true)方法。可以省略包名和类名，这样就智慧在当前包或者当前类去定位。
==注意==：必须使用#而不是空格，javadoc不想java编译器一样可以精确识别分割包和类。

如果@see后面跟着a标签，你需要指定一个超链接，可以自己指定超链接的名字label，也可不指定使用目标代码的名字或者url作为超链接名。

如果后面跟着"符号，在see also部分出现文本内容

可以添加多个@see标签，但是需要保证全部在一起
还可以将超链接放在文档中你喜欢的任何位置，使用一种特殊标签即可：`{@link package.class#feature label}`，和@see的规则一样

#### package and overview comments
在源文件中可以对类、方法、变量进行注释，然而为了生成package注释，需要在每个包目录下添加一个单独的文件。有两种方法：
1. 提供一个HTML文件命名为package.html，所有的在<body></body>标签内包裹内容会被提取。
2. 提供一个package-info.java。这个文件必须包含最开始的/\*\*...\*/包注释，紧跟着包的声明，不需要再包含多余的代码或者注释了

还可提供一个overview注释，用在所有的源文件上的，把这个文件命名为overview.html并且放在包含所有源文件的父目录下。需要使用-overview选项指定overview文件。所有的body标签下的内容会被提取出来，这些注释当用户点击导航栏中的Overview选项时展现





