---
title: Java Number
author: lijiabao
date: 2020-11-24 13:18:27.640187300 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
### Number Class


有六大类（Number子类），这六大子类和原始数据类型存在==auotoboxing==装箱（需要对象时，原始数据类型被转换为六大类的过程）和==unboxing==（六大类转换为原始数据的过程）操作。

##### 使用类而不使用原始数据类型的情况
1. 作为参数传递到需要类参数的方法中
2. 需要使用类创建的常量时，如最大值最小值
3. 用类中的类方法实现类和原始数据类型的转换时

##### Number的子类
- Byte（byte）
- Short（short）
- Integer（int）
- Long（long）
- Float（float）
- Double（double）
- BigDecimal：用于高精度计算
- BigInteger：用于高精度计算
- AtomicInteger和AtomicLong：用于多线程的应用

##### Number子类可以使用的实例方法
- `xxx xxxValue()`：返回Number类或者子类代表的原始数据类型
- `int compareTo(SubClass obj)`：和另外一个子类比较，相等返回0，小于返回-1，大于1.
- `boolean equals(Object obj)`，当参数不为null且数值相等，且是相同类型时返回true

- `static Integer decode(String s)`：decode字符串为Integer
- `static int parseInt(String s)`：解析字符串为int，只接受10进制的字符串
- `static int parseInt(String s, int radix)`：接收字符串解析，可以选择进制，可以是2，8，10，16
- `String toString()`：将数值转换为字符串
- `static String toString(int i)`：转换为特定值的字符串
- `static Integer valueOf(int i)`：将特定值转换为Integer类
- `static Integer valueOf(String s)`
- `static Integer valueOf(String s, int radix)`


##### Java的数值格式化输出
- %f、%d、%n：浮点数，10进制integer和换行符
- %td、%te：一个月中的第几天，td有前导0，te没有
- %tB：特定locale月份全名
- %ty、%tY：2位数年份和四位数年份
- %tl：12小时制的小时
- %tM：2位数的分钟数，有前导0
- %tp：特定locale的am/pm
- %tm：2位的月份，有前导0
- %tD：日期，格式位%tm%td%ty
- %08：八位数字，看情况是否有前导0
- %+：带符号，不管正还是负
- %, ：包含有特定locale的grouping字符
- %-：左靠近
- %。3：小数点后三位
- %10.3：10位，小数点后有三位

##### DecimalFormat类
用来控制==前缀、后缀、前导后导0、grouping 分隔符（千位分隔符）和decimal分隔符==。对于数值的格式化出书提供了很大的便利，但是是代码变得复杂

- ###,###.### ：123456.789的输出是123,456.789
- ###.##：123456.79是123456.789的输出
- 000000.000：000123.780是123.78
- $###,###.###：\$12,345.67，是12345.67的输出

##### 除了运算符之外的方法
**[[Math]]**

==`Math.random()`：是一个用来获取随机数的方法==