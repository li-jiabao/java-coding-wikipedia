---
title: Java String
author: lijiabao
date: 2020-11-24 14:49:06.533998100 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
##### Character类
不可变的，一旦创建就不可再更改
其和原始数据类型char存在autoboxing和unboxing

一些static methods：
- `boolean isLetter()`：判断是否是字母
- `boolean isDigit()`：判断是否是数字
- `boolean isWhitespace()`：判断是否是空格
- `boolean isUpperCase()`：判断是否是大写字母
- `boolean isLowerCase()`：判断是否是小写
- `char toUpperCase()、toLowerCase()`
- `toString()`：返回字符串

Escape Sequence：
`\t:tab,\b:backspace,\n:newline,\r:return,\f:formfeed(0x0c),\\,\',\"`

##### String
string类是不可变的，因此创建完后不可更改
创建字符串：
- `String s = "hhh";`
- `String s = new String(char[] a);`

字符串拼接：
- `String s1 += s2;`
- `String s = s1.concat(s2);`
- format格式化方式输出拼接内容：`printf()`或者`String.format();`

字符串长度：
- `String.length()`

String和Number的转换：
- `Float.valueOf(String s).floatValue()`，将获取的字符串值返回成数值
- `Float.parseFloat(String s)`，解析字符串为数值
- `String.valueOf(num)`，将数值转换为字符串
- `Float.toString(num)`

字符串操作character：
