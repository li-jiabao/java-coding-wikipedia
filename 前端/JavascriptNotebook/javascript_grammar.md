---
title: javascript_grammar
author: lijiabao
date: 2020-11-13 10:30:13.878201800 +0800
category: JavascriptNotebook
categories: JavascriptNotebook
tags: JavascriptNotebook
---
javascript语法
----
---------
#### **1. 常用数据类型**
字面量是常量，有数字(int，float，科学计数)，有字符串，表达式(用于计算)，数组(python列表)，对象(python字典)，函数

**字符串：** 拼接有加号和模板字符串，‘mynameis${name}’类似的，模板字符串用的**中文字符**的符号，字符串的属性length获得长度，属性没有括号，方法才有toUpperCase()变成大写，toLowerCase()小写，substring(start，endindex)截取片段，split()分割字符串成为数组，参数为分割字符，字符和数字相加，数字会被转为字符，运算都是先后顺序

**数组：** 创建，结构体：const arrayname = new Arrays(1， 2， 3)或者用[]即可数组，const定义的数组也可改变，push()想尾部添加元素，unshift()像头部添加元素，pop()去掉最后一项，判断变量是否为数组，Array.isArray(variables)，获取索引，indexOf()方法

**对象：** object访问值object.key，添加键值对object.key = values，新特性对象可以解构赋值，JSON.stringify()转为json字符串，对象的共享方法:valueOf()和toString()(转换为字符串类型)
空值null是对象，undefined就是undefined，不是一个东西，值相等，类型不同，可以通过设置值为null或者undefined清空对象.
typeof返回值string，number，boolean，undefined，object，function.这么几种。对象有属性和方法，属性是由键值对组成，方法则是由函数名和函数组成.
访问对象属性:object.propertyname或者object['propertyname']，访问对象方法:object.funcname()，不带括号返回函数定义

如果通过关键字new来声明变量，该变量会被创建为对象，需要避免字符串数值或者逻辑对象，会增加代码复杂性并降低运行速度

**this关键字：**在JavaScript中指的是它所属的对象

this总是指向调用该函数的对象，在全局函数中this指向window对象

- 方法中：this指的是所有者对象
- 单独情况：this指的是全局对象 [object Window]
- 函数中：this指的是全局对象，严格模式下，指的是undefined，//"use strict"值使用严格模式
- 事件中：this指的是接受事件的元素，HTML中指接受事件的html元素
call()和apply()这样的方法可将this引用到任何对象
```JavaScript
var person1 = {
	firstName: "BILL";
	lastName: "GATE";
	function_1: function {
		return this.firseName + this.lastName
	}
}
var person2 = {
	firstName: "Li";
	lastName: "jiabao"
}
person1.fullname.call(person2);  // 返回值是Lijiabao
```
```javascript
var person = {
	firstName: "Li";
	lastName: "jiabao";
	age: 22;
	fullName: function() {
		this.firstName + this.lastName
	}// 函数定义中：this 是引用该函数的拥有者，而上述的代码示例中this指代该对象。
}
```

******

#### **2. 变量**

使用关键字var定义变量，用等号为变量赋值，还有let和const，，在脚本的开头声明所有变量是个好习惯！

声明变量可跨多行，
```JavaScript
var person = "Bill Gate"， age=18;//一行声明
// 也可多行声明
var person = "Bill Gate";
age = 18;
var age;  // 声明后不赋值则为undefined
// 重复声明变量仍会保持原有变量值
var name = "Gate";
var name;
var a = "8" + 3 + 5; //result: 835: string
var a = 3 + 5 + "8": // result: 88: string
// 有条件的进行变量声明
const timezone = user.preferred_timezone || "China/Beijing"
// 比下面的方式更简洁干净
let timezone = "China/Beijing";
if (user.preferred_timezone) {
	timezone = user.preferred_timezone;
}
```
********

#### **3. 操作符与赋值运算符**
与python基本一致，文本字符串中使用\对代码进行换行。换行最好在运算符或者逗号之后，长度80以内
*******

#### **4. 运算符:**
1. 算数运算符
- ++低价--递减运算
- 字符串可用+和+=运算符来进行字符串级联
2. 比较运算符
-  == 等于
-  === 等值等型
-  != 不相等， !== 不等值或不等型
-  ? 三元运算符
3. 逻辑运算符
- && 逻辑与
- || 逻辑或
- ! 逻辑非
4. 类型运算符
- typeof() 返回变量的类型
- isinstanceof() 返回bool，判断是否是对象的实例
5. 位运算符
- & 与
- | 或
- ~ 非
- ^ 异或
| 值 | 运算符 | 优先级 |
| :-: | :-: | :-: |
| 20 | **()** | 表达式分组 |
| 19 | **.and[]** | 成员|
| 19 | **()** | 函数调用 |
| 19 | **new** |创建 |
| 17 | **++and--** | 后缀递增递减 |
| 16 | ** ++and--** |前缀递增递减 |
| 16 | **!** |逻辑否|
| 16 | **typeof** | 类型判读|
| 15 | ** \*\* ** | 幂运算|
| 14 | ** \* / % **| 乘除模数除法|
| 13 | **+ -**| 加减|
| 12 | **<<，，**| 左位移右位移，右无符号 |
| 11 | **<，，<=，=，in，instanceof** |比较运算符，对象中属性，对象实例|
| 10 | ** ==，===，!=，!==** | 相等判断|
| 9 | **&**| 按位与|
| 3 | ** =，+=，-=** | 赋值运算符 |
| 2 | **yield** |暂停函数|
| 1 | **，** | 逗号|
*******

#### **5. 语句**
用分号隔开，分号在每条可执行语句的末尾添加，另一用处是一行编写多条语句，，分号结束语句时可选的，可以不写
******

#### **6. 变量声明**
JavaScript通常以关键词开头，var关键词告诉浏览器创建一个新的变量
*****

#### **7. 标识符**
保留下来为自己所用，有关键字，数字不允许在开头出现，标识符必须以字母下划线或美元符开始

break：跳出循环，continue：跳出循环的一次迭代，try…catch：try语句出错时执行，do…while，switch：用于基于不同的条件执行不同的动作。throw：抛出生成错误。function：定义一个函数，var声明变量，debugger停至执行并调用调试函数
*****

#### **8. 注释**
//双斜杠后的内容会被浏览器忽略，/\*开始\*/结尾的可以进行多行注释
******

#### **9. 函数**

##### 函数基本内容

函数最好看作是对象，有属性和方法
语句写在函数中，函数可以重复引用，引用一个函数等于调用函数
JavaScript忽略空格，因此可以增加空格提高代码可读性
function的属性length可以返回函数定义参数个数
函数的toString()方法返回函数的定义
argument对象可以用来检测参数或者查看参数个数
在函数内部使用，argument[0]返回第一个参数，argument.length返回参数的个数
函数申明结尾一般不需要分号，因为不是可执行语句
parameter是函数定义的参数名，argument是函数接受的实际数值
函数可被用在赋值和表达式中
定义为对象属性的函数，被称为对象的方法
为创建新对象设计的函数，被称为对象构造器函数（对象构造器）

##### 函数语法

```JavaScript
// 常规定义
function function_name(para1， para2， ..) {
	执行的代码块
}
// 表达式定义
var x = function (*parameters) {代码块};
// 构造函数定义
var function_name = new Function('para'， 'para2'， 'console.log(para1 + param2)');
// 自调用函数：执行到这直接运行函数
(function () {
	代码块
})()    // 这是一个匿名自调用函数
// 箭头函数
var x = function(x,y) {return x*y;};  // ES5用法
const x = (x,y) => x * y; // ES6用法
```

##### 函数参数
parameter是函数定义的名称
argument是函数运行时传递或者接受的真实值
- **参数规则：**
不为参数规定数据类型
不对传递参数进行类型检查
不会检查接受参数的数量
- **参数默认：**
如果省略了声明参数，则丢失值被设置为undefined，有时最好为参数设置默认参数
```JavaScript
function myFunction(x, y) {
	if(y == undefined) {
		y =0;
	}
}  // 这种方法为函数定义默认参数值
```
- **arguments对象：**
函数具有一个arguments的内置对象
arguments包含函数调用时使用的参数数组
1. `argiments.length`返回参数个数
2. 类似数组访问方法`arguments[num]`可获取参数
- **参数通过值传递：**
1. 
函数通过值传递，只知道值不知道参数的位置
如果函数改变了参数的值，不会改变函数参数的原始值 
==参数的改变在函数之外是不可见的==
2. 对象时引用传递，对象引用是值
如果函数改变了对象属性，也就改变了原始值 
==对象的属性改变在外部时可见的==

##### 箭头函数

```javascript
const x = (x,y) => x * y;
```
箭头函数没有自己的this。不适合定义对象方法
箭头函数未被提升，必须在使用前进行定义
使用const比使用var安全，函数表达式始终是常量
函数是单个语句，只能省略return和大括号，最好保留他们
IE11 或更早的版本不支持箭头函数。
箭头函数的this指向外层函数作用域中的this
箭头函数的this时定义函数是所在的上下文中的this
箭头函数的this就是定义是所在的对象而不是调用时的对象

##### 函数调用与返回
在JavaScript中，始终存在一种默认的全局对象
HTML中。默认全局对象时HTML页面本身，所有上面的函数属于HTML页面
浏览器中，这个也买你对象就是浏览器创建，上面的函数字符自动成为一个窗口函数
myFunction()和window.myFunction()是同一个函数

==全局变量、方法或函数很容易在全局对象中产生命名冲突和漏洞==

- this关键字：拥有当前代码的对象，在函数中指拥有该函数的对象

- 函数调用:调用函数名而不加()会返回函数定义
1. 事件发生时，如用户点击
2. JavaScript代码调用时
3. 自调用
4. 作为方法调用
```javascript
var person = {
	name:"lijiabao",
	fullName:function{return this.name;
}};person.fullName() // 方法调用
```
5. 函数构造器调用函数
构造器调用会创建新对象。新对象会从其构造器继承属性和方法。
构造器内的 this 关键词没有值。 
==this 的值会成为调用函数时创建的新对象==
```JavaScript
function myFunction(arg1){
	this.firstName = arg1;
}
var x = myFunction();
x.firstName;  // 函数构造器调用
```

- 函数返回:
1. 函数执行到return即结束函数并返回结果，定义函数之后可以复用
2. 函数中声明的变量是局部变量，可在不同函数中定义相同变量名
3. 开始创建结束删除局部变量

##### 函数的call与apply

###### call
使用call()方法，可以编写能够在不同对象使用的方法
JavaScript中函数是对象的方法，如果怒视对象方法，那就是全局对象的函数
call()是预定义的方法，用来调用所有者对象作为参数的方法
通过call()可以使用属于另外对象的方法
```JavaScript
var person = {
firstName: "john",;astName: "jjj",
fullName: function(){return this.fistName + this.lastName;}
}
var person1 = {firstName: "li",lastName: "jiabao"}
person.fullName.call(person1);// 调用person的fullName方法
// 带参数的call()
var persons ={fullName: function(city){
return this.firstName + this.lastName + city;}};
persons.fuuName.call(person1, "hunan");
```

###### apply
通过apply()可以编写用于不同对象的方法
与call()非常类似基本用法一致，只是传参不一样，apply以数组传参数
二者区别：
1. `call()`分别接受参数
2. `apply()`接受数组形式参数，使用数组，apply非常方便
```JavaScript
// 数组中模拟max方法
Math.max.apply(null, [1,2,3]);
Math.max.apply("", [1,2,3]);
Math.max.apply(0, [1,2,3]);
Math.max.apply(Math, [1,2,3]);// 都会返回3
// 严格模式下，如果apply的第一个参数不是对象，则他将成为该调用函数的所有者
// 非严格模式下，他将成为全局对象
```

##### 函数闭包
JavaScript变量属于本地或全局作用域
全局变量可以通过闭包实现局部私有
函数可以访问函数内部定义变量，也能访问全局变量
网页全局变量属于window对象，全局变量能够被页面中的所有脚本使用和修改
JavaScript中，所有函数都有权访问他们上面的作用域
==不通过`var`创建的变量总是全局的，即使在函数中创建==
**变量的声生命周期：**
全局变量和应用程序（窗口和网页）活得一样久
局部变量活得不长，函数调用创建，结束删除

**定义：函数中定义另一个函数**
闭包是有权访问父作用域的函数，即使在父函数关闭后
JavaScript 支持嵌套函数。嵌套函数可以访问其上的作用域。
```JavaScript
function  addNum(num1， num2) {
	function doAdd() {
		return num1 + num2;
	}
	return doAdd()
}
// 计数器实例——闭包
function counterNum() {
	var counter = 0;
	function plus() {counter +=1;}
	return plus;
}
const counter = counterNum();
counter();
// 自调用也可类似闭包
var add = (function () {
	var counter = 0;
	return function () {
		return counter +=1;
	}
})();
```
*****

####  **10. HTML事件：**
发生在HTML元素上的事
- **常见的HTML事件**
| 事件 | 描述 |
| :- | :-|
| onchange | html元素已被改变 |
| onclick | html元素被点击 |
| onmouseover | 鼠标移动到元素上 |
| onmouseout | 鼠标移开元素位置 |
| onkeydown | 用户按下键盘按键 |
| onload | 浏览器完成页面的加载 |
**JavaScript能够做什么？**
事件处理程序可用于处理、验证用户输入、用户动作和浏览器动作：
- 每当页面加载时应该做的事情
- 当页面被关闭时应该做的事情
- 当用户点击按钮时应该被执行的动作
- 当用户输入数据时应该被验证的内容等等

让 JavaScript 处理事件的不同方法有很多：
- HTML 事件属性可执行 JavaScript 代码
- HTML 事件属性能够调用 JavaScript 函数
- 您能够向 HTML 元素分配自己的事件处理函数
- 您能够阻止事件被发送或被处理等等
*****

#### **11. 条件语句：**
- if...else语句
```
if (condition1) {
	condition is true ,execute this;}
else if (contion2) {
	condition2 is true，execute this;
}
else {false ,execute this;}
```
- switch语句
Switching 的细节
如果多种 case 匹配一个 case 值，则选择第一个 case。
如果未找到匹配的 case，程序将继续使用默认 label。
如果未找到默认 label，程序将继续 switch 后的语句。
```javascript
switch (表达式) {
	case n:
		代码块
		break;
	case n:
	case n: // 共享相同代码块可这样写
		代码块
		break;
	default:
		默认代码块 // 不存在case匹配运行的代码不是最后位置需用break
}// switch 使用严格比较 ===
```
- for 循环
```javascript
for (语句1; 语句2; 语句3) {
	执行代码块 // 1循环开始前执行，2定义循环条件，3循环每次执行后执行
}
```
1. 语句1：
> 通常，您会使用语句 1 来初始化循环中所使用的的变量（i = 0）
> 但情况并不总是这样，JavaScript 不会在意。语句 1 是可选的。
> 您可以在语句 1 中初始化多个值（由逗号分隔）：
2. 语句2：
> 通常语句 2 用于计算初始变量的条件。
> 但情况并不总是这样，JavaScript 不会在意。语句 2 也是可选的。
> 如果语句 2 返回 true，那么循环会重新开始，如果返回 false，则循环将结束。
> 如果省略语句 2，那么必须在循环中提供一个 break。否则循环永远不会结束。
3. 语句3：
> 通常语句 3 会递增初始变量的值。
> 但情况并不总是这样，JavaScript 不会在意。语句 3 也是可选的。
> 语句 3 可做任何事情，比如负递增（i--），正递增（i = i + 15），或者任何其他事情。
> 语句 3 也可被省略（比如当您在循环内递增值时

- for/in循环
`for (x in arr或者obj) {代码块;}`
- while循环：条件为真一致执行代码
`while (condition) {代码块;}`
- do/while循环：检查条件之前执行一次，为真继续执行
`do {代码块;} while (condition);`记得递增变量，不然循环没法结束
- 循环中的break和continue语句
- JavaScript标签：标记JavaScript语句，用标签名和冒号置于语句前
`Label:statements`,,,代码块是指{}包裹的语句
break用于跳出循环或者switch，有标签引用用于跳出任意代码块`break labelname`
continue只用于跳出循环的一次迭代

#### **12. 基本语法**

JavaScript对大小写敏感
JavaScript使用Unicode字符集
JavaScript命名规则为驼峰命名法，倾向于小写字母开头的驼峰命名法
JavaScript是在读取代码时，逐行的执行脚本代码，而传统编程语言先编译再执行JavaScrip
JavaScript逐行运行，按语句编写先后执行，代码块用{}包裹，代码块为一同执行的语句
不能使用连字符，是为减法预留的

******

#### **13.异常及处理**
`try`语句能够测试代码块中的错误
`catch`语句用来处理错误
`throw`语句用来自定义抛出错误
`finally`语句用来执行代码在`try`和`catch`后，无论如何都执行


#### **14. 技巧**
1. every和some的妙用：
`array.every(functionName)`数组里面元素的函数返回值全为true返回true
`array.some(functionName)`只要有一个为true就返回true
2. 有条件的设置变量：
`const timezone = user.preferred_timezone || "China/Beijing"`
3. 数组利用map进行转换：
`array.map(Number);array.map(functionName)`
4. 对象解构：
```JavaScript
var person = {firstNmae: "Li",lastName: "jiabao"};
const firstName = person.firstName
// 利用对象解构
const { firstName, lastName} = person;
// 直接访问变量名就好
console.log(firstName);
```
5. console控制台函数有许多好用的调试功能：
```JavaScript
// console的time可以查看代码运行时间，time和timeEnd函数参数提供相同字符串即可
console.time('loop')
for (i=0; i<10; i++) {dosome;}
console.timeEnd('loop')
```
6. 过滤出唯一值（ES6）：利用set集合
```javascript
const array = [1,1,3,3,2,1,4,5];
const soloArray = [...new Set(array)];
console.log(soloArray);
```
7. 与或运算：
```javascript
// 三元运算符编写简单条件语句的快速方法
x > 10 ? "above 10" : "below 10";
x > 10 ? (x > 20 ? "above 20" : "between 10 & 20") : "below 10";
// 当三元运算符比较复杂时，可以用&& 和 ||运算符
// && return value when condition false, or, return last value
ler one = 1, two = 2, three = 3;
console.log(one && two && three); // return 3
// || return value when condition true, or, return last value
console.log(one || two || three); // return 1
// || can use to get variable's length,when don't kown type
console.log((variableName || []).length);
console.log(this.state.data || "not have this property");
// new property ,optional chaining appended ? operator
// ? first judge front value,null or undefined,end call return null or undefined
this.state.date?.();this.state?.data;
```
8. 转换为布尔值、字符串和数字：
```JavaScript
// convert to boolean
const isTrue = !0, isFalse = !1, alsoFalse = !!0;
// convert to string
let strconvert = 1 + "";
// convert to number
let numconvert = +"15",num1 = +true;
// if +operator don't work ,and you want int number,use ~~operator
let numconvert = ~~"15"; 
```
9. `**`幂运算符,ES7出现的，之前只有2的幂运算有简写，`2 <<(n-1) ==2**n`,`Math.pow()`是常规的幂运算使用方法
10. 快速浮点数转整数，利用位或运算符，如果数字为整数，向下取整，为负，向下取整，实际就是将小数位舍去，还可用`~~`来取得相同的效果。
```javascript
console.log(23.9 | 0); // 23
console.log(-23.9 | 0); // -23
console.log(~~23.9,~~-23.9); //23 -23
// delete last number
console.log(Number("1553".substring(0,"1553".length-1)));
console.log(1553/10 | 0, 1553/100 | 0, 1553//1000 | 0);
```
11. 类中的自动绑定：在ES6使用箭头表示法，通过这样的操作隐含绑定，为类构造函数保存几行代码，告别重复的表达式`this.myMethod = this.myMethod.bind(this)`
```JavaScript
import React, { Component } from React;
export default class App extends Component {
	constructor(props) {
	super(props);
	this.state = {};
	}
myMethod = () => {
	// this method is bound implicitly!
}
render() {
return (
	<>
		<div>
			{this.myMethod()}
		</div>
	</>
	)
}
};
```
12. 数组截断：没有python[0:3]类似操作，只能切片函数或者改变length属性
```javascript
const array = [1,4,55,6,5,4,7,8,9];
array.length = 4; // result is [1,4,55,6]
// slice()
array = array.slice(0,4); // velocity/tempo/rate is faster
// array's last number
array = array.slice(-1);array = array.slice(-2);
```
13. 格式化JSON：
`console.log(JSON.stringify({1:2,3:4}, null, "\t"))`
`JSON.stringify()` is use for standardize json or object type






