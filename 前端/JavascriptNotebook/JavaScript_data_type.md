---
title: JavaScript_data_type
author: lijiabao
date: 2020-05-17 21:31:48.359101200 +0800
category: JavascriptNotebook
categories: JavascriptNotebook
tags: JavascriptNotebook
---
JavaScript数据结构
==

#### 字符串
JavaScript字符串是引号中的零个或多个字节
```JavaScript
var name = "lijiabao";
var car = 'AUDIO';  // 字符串可使用单双引号定义
var nation = "China is a beautiful country "china"";
// 会出错,这时可使用\转义字符如:\",或者使用不与字符串定义匹配的单引号.
var str_length = name.length; // 字符属性length返回长度
```
******

##### **格式化字符串**
```javascript
`${variable or expression or functionName or objectName}`
```

***********
##### **字符串方法**
- 字符串长度：length属性
- 字符串指定文本出现索引：首次出现，indexOf()方法。最后出现，lastIndexOf()方法未找到返回-1，可以设置第二个参数(起始查找位置)
- 搜索特定值字符串并返回匹配位置：search()，可以设置更强大的搜索值（正则）
- 提取部分字符串
> slice(start, end)提取部分并返回给新字符串，起始索引和中止索引之间字符，可接受负值索引，如果忽略第二参数，直接提取到之后的所有（负索引不支持ie8及之前）
> 
> substring()类似slice，不过不支持负索引
> 
> substr() 类似slice()不过第参数是提取长度，如果省略第二参数，都是直接取到最后
- 替换字符串 replace()
> 第一参数是原有，第二是替换字符，不改变原有字符串，而返回新字符串，
> replace()只替换首个匹配且大小写敏感
> 正则i标志【/string/i】可替换大小写不敏感，，正则g标志【/string/g】替换全部匹配*
- 大小写转换：toUpperCase()和toLowerCase()
- 字符串连接：concat()连接两个或者多个字符，可替代加法运算符，不改变原有字符，字符串不能更改只能替换
- 删除两端空白符：trim()，ie8不支持，可用正则搭配replace实现
```javascript
if (!String.prototype.trim) {
  String.prototype.trim = function () {
    return this.replace(/^[\s\uFEFF\xA0]+|[\s\uFEFF\xA0]+$/g, '');
};// 把replace实现的函数trim添加到原型String.prototype中
var str = "       Hello World!        ";
alert(str.trim());
```
- 提取字符串字符安全的方法：charAt(下标)返回指定下标字符串，charCodeAt()返回字符的Unicode编码，charAt()找不到返回空字符，[]返回undefined
- 字符串也支持[]属性访问，但不可更改，`str[0] = 'a'`不工作也不报错
- 字符转为数组：split(para：分割字符)
******

#### 数字
只有一种数值类型，带不带小数点均可，也可科学计数法，数值始终是64为浮点数

JavaScript 数值始终以双精度浮点数来存储，根据国际 IEEE 754 标准。

此格式用 64 位存储数值，其中 0 到 51 存储数字（片段），52 到 62 存储指数，63 位存储符号 

整数精确15位，小数最大17位，小数计算并部十分精准使用乘除可消减不精准问题 

数字与字符相加会被视为字符再运算，但运算顺序从左至右 

在数字运算中(加法运算之外的)，会尝试将字符转为数字计算 

JavaScript的NaN是非数值，数字与字符相除不了返回NaN，全局函数isNaN()判读 

Infinity（-Infinity）是计算超出最大可能数的返回值，Infinity和NaN二者类型均为number

JavaScript会把前缀为0x的数值常量解释为十六进制

进制转换可用toString()传入进制的数字
******
##### **数值方法**
- 以字符串返回数值：toString()，也可传入数字转换进制
- 返回四舍五入的指数计数法数字的字符串值：toExponential()参数传入小数点后字符数
- 返回指定参数位数小数数值字符串：toFixed()，参数传入小数点位数
- 返回指定参数长度的字符串值：toPrecision()，不传参默认全部返回
- 数值返回数值：valueOf()可将数值对象转为原始值
*******
###### 全局方法
适用所有数据类型
| 方法 | 描述 |
| Numbenr() | 返回数字，不能返回则为NaN，还可把日期转为数字 |
| parseFloat()和paeseInt() | 返回浮点数，无法返回则为NaN，允许空格返回首个数字 |
**数值属性：**不可用于变量，Number.属性名即可
| 属性 | 描述 |
| MAX_VALUE | 返回可能的最大数 |
| MIN_VALUE | 返回可能的最小数 |
| POSITIVE_INFINITY | 表示无穷大（溢出返回） |
| NaN | 表示非数值 |
| NEGATIVE_INFINITY | 表示无穷小（溢出返回）|
*****
##### **运算符**
JavaScript将数字存储为64浮点数，但所有位按32位二进制执行，执行位运算前，JavaScript将数字转换位32位有符号整数，按位操作后，将数字转换回64JavaScript数
JavaScript使用32位有符号整数，有符号整数最左边的位作为减号。`~5`返回`-6`

| 运算符 | 名称 | 描述 |
| ------ | ------ | ------- |
| & | AND | 一对数位，两位都为1返回1，否则0 |
| \| | OR | 两位之一为1，返回1 |
| ^ | XOR | 只有一位1，返回1 |
| ~ | NOT | 反转所有位 |
| << | 零填充左位移 | 从右推入零向左位移，推出左边的位数 |
| >>> | 零填充右位移 | 从左推入零向右位移，最左边推出 |
| >> | 有符号右位移 | 从左推入最左位的拷贝向右位移 |
```javascript
// 十进制转二进制
function dec2bin(dec) {
	return (dec >>> 0).toString(2);
}
// 二进制转十进制
function bin2dec(bin) {
	return parseInt(bin, 2).toString(10);
}
```
********

#### 数组
**数组是对象**
##### 数组基本内容
创建：
```javascript
var cars = [car1, car2,car3];  // 最后一个不需要逗号
var cars = new Array(car1, car2, car3);  // 最好用第一种方法
```
访问数组元素：`cars[0]`数字为元素的下标，索引从0开始，数组名访问所有元素
改变数组元素：`cars[0] = 'AUDIO';`
数组不接受负值索引，访问最后一个元素用`array[array.length-1];`
遍历数组元素：最安全方法使用`for`循环,也可`Array.foreach()`函数
```javascript
var fruits, text, flen, i;
fruits = ['banana', 'mongo', 'pear'];
flen = fruits.length;
text = '<ul>';
for (i = 0; i < flen; i++) {
	text += '<li>' + fruits[i] +'</li>';
}
text += '<ul>';
```
添加数组元素：最佳方法是`push()`方法。也可`array[array.length] = 'mongo'`
如果使用高索引会创建未定义的‘洞’，即undefined元素。`array[10] = 'mongo'`
关联数组：JavaScript不支持命名索引数组，`var person = [];person['name'] = 'li';// 不可`，JavaScript只支持数字索引。
数组和对象区别是数组使用数字索引，对象使用命名索引
可以使用`Array.isArray(array)`判断是否是数组
******
##### **数组方法**
- 转换为数组值为逗号分割字符串：`toString()`方法，如需原始值，或自动将数组变成字符串，而不需使用`toString()`,是所有对象都有的方法
- 结合为一个字符串：`join()`参数为连接字符
- 删除数组值：`pop()`删除数组最后一个值并返回
- 添加元素：`push()`数组结尾添加一个值
- 位移元素：`shift()`删除首个元素并将其他元素位移到更低索引并返回删除值
- 开头添加元素：`unshift()`开头添加，并将其他元素往后位移，返回新数组长度
- 更改元素：`array[index] = 'change_value'`或利用`length`属性
- 删除元素：`delete()`会留下空洞，最好用`pop()`或者`shift()`
- 合并数组：`concat()`可以接受多个数组参数，返回新数组
- 拼接数组：`splice()`参数有添加个数，删除个数，添加的值splice(0,1)可删除第一个值
- 裁剪数组：`slice()`与字符的slice()类似
******
##### **数组排序**
- 以字母顺序排序：`sort()`对数值排序会产生错误,可用`array.sort(function(a, b){return a - b})`升序排列，b-a降序排列，比值函数用来定义一种排序顺序，随机排序，可引用Math的random方法返回随机数去进行比较{return 0.5 - Math.random()}，数组包含对象仍可用该种方法，知识修改一下内容即可
- 反转数组：`reverse()`
- 查找最大最小值：Math.max.apply(null,array),Math.min.apply()
- 最快的方法可以自建：
```javascript
function myMaxArray(arr) {
	var len = arr.length;
	var max = -Infinity;
	while (len--) {
		if (arr[len] > max) {
			max = arr[len];
		}
	}
	return max
}
```
*******

##### **数组迭代方法**
- `Array.forEach(functon_name)`方法：为每一个人数组元素调用一次函数，内部函数接受三个参数，value，index，arry
- `Array.map(function_name)`方法：对每个数组元素执行函数创建新数组，不改变原数组，对没有值的数组不执行函数
- `Array.filter()`方法：创建一个通过测试数组元素的新数组，同样接受三个参数
- `Array.reduce()`方法：不改变原数组，从左至右工作，接受四个参数，totol，其他参数同上。用以减少数组值，可接受初始值，即为totol的开始值``array.reduce(func, totol_primary)`
- `Array.reduceRight()`：从右至左运行，其他同reduce
- `Array.every()`：检查数组所有元素是否通过测试，三个参数
- `Array.some()`：检查某些数组是否通过测试，三个参数
- `Array.indexOf()`：搜索元素在数组中的位置，从零开始，参数为：item和start
- `Aarry.lastIndexOf()`：最后元素开始，其他同indexOf()
- `Array.find()`：查找并返回通过测试的第一个元素，三个参数同map()，未找到返回undefined
- `Array.findIndex()`：返回满足条件的值的索引，其他同find()
*****

#### 日期
##### 日期基本内容
日期输出是按浏览器的失去并显示为全文文本字符串，日期对象是静态的
创建：
```javascript
new Date();// JavaScript日期存储为毫秒相对1970.1.100:00计算
new Date(year, month, day, hours, minutes, seconds, milliseconds);
// 参数一个视为毫秒参数，两个到七个则总年开始往后数即可
new Date(milliseconds);// 月份从0开始为一月，一位两位的年份被解释为19**年
new Date(datestring); // 从日期字符串创建日期对象
```
Date对象在html中自动调用toString()显示全文文本日期字符串
`toUTCString()`将日期转换为UTC字符串
`toDateString()`将日期转换更易读的形式
******
##### **日期格式化：**
iso格式遵循严格标准，其他格式不明确可能浏览器特定的
| **类型** | **实例** | **格式标准** |
| ----- | ------ | ------|
| ISO日期 | "2020-04-18"(国际标准) | `Date('YYYY-MM-DD')`可不规定月日，也可加时分秒，用T隔开时间和日期 |
| 短日期 | "04/18/2020"or"2020/04/18" | `MM/DD/YYYY`格式，不带前导零可能出错 |
| 长日期 | "Ari 18 2020" or "18 Apr 2020" | `MMM  DD YYYY`格式，月天可任意调换逗号忽略，大小写不敏感 |
| 完整日期 | "Saturday April 18 2020" | 完整JavaScript格式字符串，会忽略日期名称和一些错误 |
******
##### **日期获取方法：**
获取的信息来自日期对象
| **方法** | **描述** |
| ----- | -----|
| getDate() | 以数值返回天（1-31） |
| getDay（） | 以数值返回周名（0-6） |
| getFullYear() | 获取四位的年yyyy |
| getHours() | 获取小时（0-23） |
| getMilliseconds() | 获取毫秒（0-999）|
| getMinutes() | 获取分钟（0-59） |
| getSeconds() | 获取秒（0-59） |
| getMonth()  | 获取月（0-11） |
| getTime() | 获取时间（1970.1.1至今）返回毫秒数 |
UTC时间方法在上述方法的get后面接上UTC即可
日期的设置方法与获取类似，只是将get换成set即可
日期很容易的进行比较，date1 >  date2，返回true或false
*****

#### Math对象
##### 属性和方法
Math对象允许执行数学任务，里面包括许多的数学相关的运算符常量以及求和取整等方法
| 方法或属性 | 描述 |
| ------ | ------- |
| Math.PI | 返回Π值 |
| Math.round() | 四舍五入取整|
| Math.pow(x,y) | x的y次幂 |
| Math.sqrt() | 返回平方根 |
| Math.abs() | 返回绝对值 |
| Math.floor() | 向下取整 |
| Math.ceil() | 向上取整 |
| Math.min()and max() | 最大最小 |
| E,PI,SQRT2,SQRT1_2,LN2,LN10,LOG2E,LOG10E | 欧拉指数，圆周率，2韦迪的e的对数 |
*******

#### 随机
- Math.random()：返回[0,1)之间的随机数
- Math.floor(Math.random()*num)：返回[0,num)的随机整数
```javascript
function getInterRand(max, min) {
	return Math.floor(Math.random()*(max - min) + min);
} // 自建获取某一范围的随机整数
```
******

#### 布尔类型
**true和false**
可用Boolean()函数来确定表达式是否为真
0和false和null和undefined和NaN和-0都为布尔类型的false
比较运算符：===和==和！=和！==和大于大等于小于小等于
逻辑运算符：&&与||或！非
条件运算符（三元运算符）：
`vaiablename = (condition) ? value1:value2`
*****

#### 对象
JavaScript中对象是王
几乎所有事物都是对象，除了原始值
原始值指的是没有属性或者方法的值，原始数据类型值的是拥有原始值的数据类型
有五种原始数据类型：boolean，string，number，null，undefined
- 布尔数值和字符如果用new定义的都是对象
- 日期永远是对象
- 算术永远是对象
- 正则表达式永远是对象
- 数组函数和对象永远是对象
******
对象是包含变量的变量，对象属性是对象中的命名值，对象方法是指对象执行的动作，对象方法是包含函数定义的对象属性，对象是属性和方法的命名值的容器。
对象创建：JavaScript变量不易变，对象才是易变的
```javascript
var person = {};
var person = new Onject(); // 尽量不用
var a = {a:'jjj'};
var foo = Object.create(a);  // 这种构造对象的方法是将a作为foo的原型创建
var foo = Object.create(null)  // 创建一个没有原型的对象
// 或者定义对象构造器然后创建构造类型的对象
// ECMAScript可用Object.create()创建
```
*****
##### **JavaScript属性**

- 访问：`objectName.propertyName;objectName["propertyName"]`或者`objectName[expression];// expresion最终得到的得是属性名`或者for/in循环遍历`for (variable in objectName) {代码块;}`
- 添加：可直接赋值即可`objectName.propertyName = values;`
- 删除：delete关键字`delete objectName.propetyName;`
> delete删除属性的值和属性本身，删除后，添加回来前无法使用
> delete设计为作用于对象属性，对变量或者函数没有影响
> 不应用于预定义的JavaScript对象属性
> delete不会删除被继承的属性，但如果删除了原型属性，会影响原型继承的属性
- 属性值：只有属性值可修改，属性是可读的
*****

##### **方法**
对象的方法指在对象上执行的动作，包含函数定义的属性
- 访问：`objecname.methodName()`用这种访问方法如果不带括号，返回函数定义
- 使用内建方法：如String对象的toUpperCase()
- 添加新的方法：在构造函数内部完成`funcName = function (){}`
*****

##### **this关键字**
被称为this的对象是拥有改代码的对象，函数中是指拥有该函数的对象
this 的值，在对象中使用时，就是对象本身。
在构造器函数中，this 是没有值的。它是新对象的替代物。 当一个新对象被创建时，this 的值会成为这个新对象。
请注意 this 并不是变量。它是关键词。您无法改变 this 的值。
*****
##### **javascript对象访问器**
引入了Getter和Setter，允许定义对象访问器，利用set和get关键字定义，如：
```javasxript
//Setter对象访问器的定义
var person = {
"firstName": "bill","lastName": "Gates","language": "en",
set lang(lang) {return this.language = lang};
//Getter对象访问器的定义
var person = {
"firstName": "bill","lastName": "Gates","language": "en",
get lang() {return this.language};
}
// 调用的时候直接类似属性调用一样即可
// 函数与访问器区别在于后者语法简便，可以确保更好的数据质量，有利于后台工作
// Object.defineProperty()也可用于添加Setter和Getter
Object.defineProperty(objectName, getterName, {
	get function() {}
});
```
******

##### **javascript对象构造器**
对象类型（蓝图）（类）
大写首字母对构造器函数命名
```javascript
// 对象构造器函数，利用new调用构造器函数创建相同类型的对象
function Person(first, last) {
	this.firstName = first;
	this.lastName = last;
}
class Person {
	constructor(first, last) {
		this.firstName = first;
		this.lastName = last;
	}
	funcName(*args) {
		return argument[0] + argument[1]
	}
}
var me = new Person("li", "jiabao");
// 添加属性很简单，与常规对象一样
me.nation = "China";// 属性只在当前对象添加
// 添加方法
me.name = function() {
	return this.firstName + this.lastName;
};// 同样只在当前对象添加
// 构造器添加属性和方法并不能如此，只能在构造器函数重新添加
```
##### **内建构造器**
- Stirng()
- Number()
- Boolean()
- Array()
- Object()
- RegExp()
- Function()
- Date() // Math()对象不在此列，其是全局对象，new不可用于Math

尽管JavaScript有内建构造器，但推荐使用：
- {}代替new Object()
- ""代替new String()
- 数值字面量代替Number()
- 布尔字面量代替Boolean()
- []代替Array()
- 模式字面量代替RegExp()
- () {} 代替Function()
******
##### **对象原型**
所有的JavaScript对象都从原型继承属性和方法
1. 原型继承：
> 日期对象继承自Date.prototype,数组继承Array.prototype,新建对象继承Object.prototype
> 所有的对象都继承自Object.prototype
2. prototype属性添加新属性新方法
```javascript
function Person(first, last) {
	this.firstName = first;
	this.lastName = last;
}
Person.prototype.nation = "China";// 添加新属性
Person.prototype.name = function() {
	return this.firstName + this.lastName;
}; // 添加新方法
// 请只修改自己的原型，绝不要修改JavaScript对象的原型
```
******

##### **JavaScript ES5对象方法**
###### **新的对象方法**
```javascript
// 添加或更改属性
Object.defineProperty(object, property, descriptor)
Object.defineProperty(me, language, {value: "ZH"})
// 添加或更改多个属性
Object.defineProperties(object, descriptors)
// 访问属性
Object.getOwnPropertyDescriptor(object, property)
// 以数组返回所有属性
Object.getOwnPropertyNames(object)
// 以数组返回所有可枚举的属性
Object.keys(object)
// 访问原型
Object.getPrototypeOf(object)
// 阻止向对象添加属性
Object.preventExtensions(object)
// 如果可将属性添加到对象，返回true
Object.isExtensible(object)
// 防止更改对象属性（不是值）
Object.seal(object)
// 如果对象被密封，返回true
Object.isSealed(object)
// 防止对对象进行任何修改
Object.freeze(object)
// 如果对象被冻结，返回true
Object.isFrozen(object)
```
###### **属性元数据**
ES5允许更改以下属性元数据：
```JavaScript
writable : true    // 属性可修改
enumerable : true   // 属性可枚举
configurable : true  // 属性可配置
writable : false   // 属性不可修改
enumerble : false   // 属性不可枚举
configurable : false  // 属性不可重新配置
// 实例，设置属性为只可读，其他的属性设置类似
Object.defineProperty(person, nation, {writable: fasle})
```
###### **设置访问对象访问器**
```javascript
Object.defineProperty(objectName, getterName, {
	get function() {}
});
```
******

#### 数据类型转换
数据类型：function，object，boolean，number，string，undefined
对象类型：Date，Object，Array，null
Number()转换数值，String()，转换为字符串，Boolean()转换为布尔类型
- typeof 确定数据类型，不能确定数组和日期只返回object
`typeof "string";typeof Date();typeof array`
- constructor 属性返回变量的构造器函数，可用于判断数组
`array.constructor.toString().indexOf('Array') > -1`
`array.constructor === Array;`
- 当试图输出对象或者变量时，JavaScript自动调用toString()方法，数字方法toString()也可将数字转换成字符，全局方法String()可将大部分数据转换册成字符，日期转换成字符可以利用日期方法
- 字符转数值可用Number()方法，也可用+运算符，无法运算转换成NaN类型，布尔类型和日期数据也可转换。
- JavaScript拥有自动的类型转换，但结果并不一定是你期望的
********

#### 正则表达式
构成搜索模式的字符序列，可用于文本搜索和文本替换操作

##### 语法
直接量：`/pattern/modifiers;`，pattern是搜索是使用的模式，modifiers或attributes是修饰符，可以设置大小写不敏感之类的。
创建RegExp对象的语法：`new RegExp(pattern, attributes);
*******

##### 返回值
一个新的RegExp对象，具有指定的模式和标志，如果参数是正则表达式而不是字符串，那么，RegExp()构造函数将用与指定的RegExp()相同的模式和标志创建一个新的 RegExp对象
如果不用new，而将RegExp()当成函数调用，与new调用一样，只是pattern位正则表达式时，只返回pattern，而不创建xindeRegExp对象。
******

##### 异常
pattern不是合法正则表达式，或attribute含有"i-g-m"外的字符，抛出语法错误
如果pattern是RegExp对象，但没有省略attribute参数，抛出类型错误
*******

##### **修饰符：**
| 修饰符 | 描述 |
| ------ | ------ |
| i | 执行时大小写不敏感的匹配 |
| g | 执行全局匹配而非找到第一个就结束 |
| m | 执行多行匹配 |
********

##### **正则表达模式**
括号用于查找一定范围的字符串
| 表达式 | 描述 |
| ---- | ----- |
| [abc] | 查找方括号里的任意字符 |
| [^abc] | 查找任何不在括号的字符 |
| [0-9] | 查找0到9的数字 |
| (a或b) | 查找由或符号分割的任何选项 |
*******

##### **元字符**
指拥有特殊含义的字符：
| 元字符 | 描述 |
| ---- | ----- |
| . | 查找单个字符，除了换行符和行结束符 |
| \w | 查找单词字符 |
| \W | 查找非单词字符 |
| \d | 查找数字字符（大写相反） |
| \s | 查找空白字符（大写相反） |
| \b | 匹配单词边界（大写相反） |
| \0 | 查找NUL字符 |
| \n | 查找换行符 |
| \f  | 查找换页符 |
| \r | 查找回车符 |
| \t | 查找制表符 |
| \v | 查找垂直制表符 |
| \xxx | 查找八进制xxx规定的字符 |
| \xdd | 查找十六进制dd规定的字符 |
| \uxxxx | 查找十六进制xxxx规定的Unicode字符 |
*******

##### **量词**
| 量词 | 描述 |
| ---- | ---- |
| n+ | 至少一个 |
| n\* | 零个或多个 |
| n? | 零个或一个 |
| {x} | x个 |
| {x, y} | x到y个 |
| {x, } | 至少x个 |
| n$ | n结尾的字符 |
| ^n | n开头的字符 |
| ?=n | 任何其后接着字符串n的字符串 |
| ?!n | 任何其后没有接字符串n的字符串 |
*******

##### ** RegExp对象的属性**
| 属性| 描述 |
| ---- | ---- |
| global | RegExp对象是否有标志g |
| ignoreCase | 是否有标志i |
| lastIndex | 一个整数，表示下一次匹配的字符位置 |
| multiline | 是否有标志m |
| source | 正则表达式的源文本 |
*******

##### **RegExp对象的方法**
| 方法 | 描述 |
| ---- | ---- |
| compile | 编译正则表达式 |
| exec | 检索字符串中指定的值，返回找到的值并确定位置 |
| test | 检索字符串中指定的值，返回true或false |
******

##### **支持正则表达式的String对象的方法**
| 方法 | 描述 |
| ---- | ----|
| search | 检索与正则表达式相匹配的值 |
| match | 找到一个或多个正则表达式的匹配 |
| replace | 替换与正则表达式匹配的字串 |
| spilt | 把字符串分割位字符串数组 |
*******

##### **实例**
申明正则表达式对象：
`/W3CSCHOOL/i;`
`const regexps = new RegExp('/W3CSCHOOL/', "i");`
`test()`使用:
`regexps.test("i love w3cschool")`
`exec()`使用：
`regexps.exec('i love w3cschool')`



