---
title: Java basic
author: lijiabao
date: 2021-02-03 23:25:55.079525400 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
## Java basic

### Java Enviroment Variable
JAVA_HOME：Java的JDK安装路径
CLASSPATH：Java的包查找路径
PATH：Java的可执行文件路径，使得可在命令行执行java编译和运行

### name rule
以字母、美符或者_开始，之后可以是数字、字母、下划线和美元符的组合。关键字不可以用来命名。

### Name convention
- Java大小写敏感
- methodName: 只有一个单词时，全小写，如果好几个单词，后面单词的首字母大写。一般方法命名是：动词+形容词/名词
- ClassName：只有一个单词首字母大写，其他单词首字母大写。一般的类名是：名词+名词
- variableName：和方法名类似,名词+名词。如果是常量，全大写并且不同单词之间使用"\_"连接。

### basic rule
- 一个源文件中从`public static void main(String[] args){}`开始执行
- 一个源文件中只能声明一个public类，但是可以有多个非public的类。
- 在一个源文件中，package声明必须在最开始一行，如果有import语句，则需放在package和类之间。
- 源文件的文件名需和源文件中的public类的类名一致，无package时import在首行
- package和import语句对整个源文件中的类都有效，但是不能对源文件中不同类有不同的package声明

### [[Java modifier]]（identifier）
- access modifier：public，default，private，protected
- non-access modifier：static，final，abstract，synchronized，volatile，transient

### [[Java variable]]
- static variable：以static关键字定义的变量，在类中，方法外。类变量每个类只有一份拷贝。静态变量的访问建议使用`className.staticName`访问。**程序结束后销毁**。
- instance variable：不以static关键字声明的在方法外类中的变量。不同类的实例拥有各自的实例变量。可以直接变量名访问，也可以`this.instanceName`访问。**对象销毁时变量销毁**。
- local variable：定义在block中的变量，仅在块体中可以访问，**语句执行完成后变量销毁**。局部变量不可以使用访问控制符。

### Java comment
- 单行注释：//，，，/**/
- 多行注释：/\*\*\n\n\*/


### Java parameters
- 隐式参数：就是类变量，this关键字代替，指向的是对象
- 显式参数：方法中实际显示表现出来的参数