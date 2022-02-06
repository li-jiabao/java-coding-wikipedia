---
title: python_knowledges
author: lijiabao
date: 2020-11-23 14:50:10.984747000 +0800
category: python_notebook
categories: python_notebook
tags: python_notebook
---
# python知识

### os模块
#### os的常用函数
1. `os.getcwd()`获取当前进程下的工作目录
2. `os.name`当前系统的名字,nt是windowsposix表示linux
3. `os.chdir()`更改当前工作目录
4. `os.mkdirs()`递归创建文件夹,需要求有子目录`os.mkdirs(r'C:\Users\shinelon\Desktop\hhh\1122', mode=0o777)`
5. `os.mkdir()`创建文件夹
6. `os.listdir(path)`列出当前路径下的所有文件和文件夹
7. `os.remove()`删除指定目录的文件,如果是目录会报错
8. `os.rename(src, dst)`文件重命名
9. `os.renames(old,new)`类似于rename但是可以命名文件的上级目录
10. `os.linesep()`获取当前系统的行分隔符
11. `os.pathsep()`获取分割搜索路径的分隔符
12. `os.close()`关闭文件描述符
13. `os.stat()`获取文件或者目录信息
14. `os.sep()`获取系统的路径分隔符
15. `os.path(abspath)`获取绝对路径
16. `os.path.basename()`获取文件名
17. `os.commonprefix()`所有path路径最大的共有路径
18. `os.path.dirname(path)`获取文件路径
19. `os.path.exists(path)`判断文件路径是否存在
20. `os.path.lexists()`存在或者损坏返回true不存在返回false
21. `os.path.expanduser()`将包含~和~user路径切换为user目录
22. `os.path.expandvars()`根据环境变量切换包含的$name和${name}
23. `os.path.getatime()`返回最近访问时间的秒数
24. `os.path.getmtime()`返回最近修改时间的秒数
25. `os.path.getctime()`返回创建时间秒数
26. `os.path.getsize9)`返回文件大小,不存在报错
27. `os.path.isabs()`判断是否为绝对路径
28. `os.path.isfile()`判断是否为文件
29. `os.path.isdir()`判断是否为文件夹
30. `os.path.join()`合并文件名,自动添加文件分隔符
31. `os.path.normcase(path)`转换路径 的大小写和斜杠
32. `os.path.normpath()`规范文件路径
33. `os.path.realpath()`返回path的真实路径
34. `os.path.relpath()`返回到达该路径文件的相对路径
35. `os.path.samefile()`判断目录或文件是否相同
36. `os.path.split()`路径分割为dirname和basename
37. `os.path.splitdrive()`路径分割为驱动器和路径的元组
38. `os.path.splitext()`返货路径名和文件扩展名
39. `os.path.walk()`遍历文件的路径,遍历结果是dirs,folders,files,文件路径,文件路径下的文件夹,所有文件
#### python对接shell的方法
**1. `os.system`**
该函数返回命令执行结果的返回值，system()函数在执行过程中进行了以下三步操作：

> 1. fork一个子进程；
> 2. 在子进程中调用exec函数去执行命令；
> 3. 在父进程中调用wait（阻塞）去等待子进程结束。

> 对于fork失败，system()函数返回-1。
> 由于使用该函数经常会莫名其妙地出现错误，但是直接执行命令并没有问题，所以一般建议不要使用。

**2. `os.popen`**
`popen()` 创建一个管道，通过fork一个子进程,然后该子进程执行命令。返回值在标准IO流中，该管道用于父子进程间通信。父进程要么从管道读信息，要么向管道写信息，至于是读还是写取决于父进程调用`popen`时传递的参数（w或r）。通过`popen`函数读取命令执行过程中的输出示例如下：

```
#!/usr/bin/python
import os

p=os.popen('ssh 10.3.16.121 ls')
x=p.read()
print(x)
p.close()
```
**3. `subprocess`模块**
1）概述
  `subprocess`模块是在2.4版本中新增的，官方文档中描述为可以用来替换以下函数：
`os.system、os.spawn、os.popen、popen2`
2）参数
官方对于``subprocess`模块的参数解释如下：

> args is required for all calls and should be a string, or a sequence of program arguments. Providing a sequence of arguments is generally preferred, as it allows the module to take care of any required escaping and quoting of arguments (e.g. to permit spaces in file names). If passing a single string, either shell must be True (see below) or else the string must simply name the program to be executed without specifying any arguments.

参数既可以是string，也可以是list。
```
subprocess.Popen([“cat”,”test.txt”])
subprocess.Popen(“cat test.txt”, shell=True)
对于参数是字符串，需要指定shell=True
```
3）使用示例
其中==subprocess.call==用于代替os.system，示例：
```
import subprocess
returnCode = subprocess.call('adb devices')
print(returnCode)
subprocess.check_output
```
==使用subprocesss.run()==
```python
import subprocess

completed = subprocess.run(['ls', '-1'])
print(completed.returncode)
```
**subprocess.Popen的使用**

1.执行结果保存在文件

```
cmd = "adb shell ls /sdcard/ | findstr aa.png"
fhandle = open(r"e:\aa.txt", "w")
pipe = subprocess.Popen(cmd, shell=True, stdout=fhandle).stdout
fhandle.close()
```
2.执行结果使用管道输出
```
pipe=subprocess.Popen(cmd,shell=True,stdout=subprocess.PIPE).stdout
print pipe.read() 
```
**4. `commands.getstatusoutput()`**
使用``commands.getstatusoutput()`` 方法就可以获得到返回值和输出：

```
status, output = commands.getstatusoutput('sh hello.sh')
print(status, output)
```

### time模块

`time.time()`获取时间戳
`time.localtime(time.time())`获取当地时间的结构化字符串
`time.asctime(time.time())`将时间戳转化为可识别的易读时间表达式
`time.strftime('%Y-%m-%d %H:%M:S', time.localtime())`将当地时间转换为特定的时间字符串模式
`time.strptime('2015-12-22 15:33:55')`将格式化时间字符转换为时间戳

**每个时间戳都以自从1970年1月1日午夜（历元）经过了多长时间来表示。**

------

#### 什么是时间元组？

很多Python函数用一个元组装起来的9组数字处理时间:

| 序号 | 字段         | 值                                   |
| :--- | :----------- | :----------------------------------- |
| 0    | 4位数年      | 2008                                 |
| 1    | 月           | 1 到 12                              |
| 2    | 日           | 1到31                                |
| 3    | 小时         | 0到23                                |
| 4    | 分钟         | 0到59                                |
| 5    | 秒           | 0到61 (60或61 是闰秒)                |
| 6    | 一周的第几日 | 0到6 (0是周一)                       |
| 7    | 一年的第几日 | 1到366 (儒略历)                      |
| 8    | 夏令时       | -1, 0, 1, -1是决定是否为夏令时的旗帜 |

上述也就是struct_time元组。这种结构具有如下属性：

| 序号 | 属性     | 值                                   |
| :--- | :------- | :----------------------------------- |
| 0    | tm_year  | 2008                                 |
| 1    | tm_mon   | 1 到 12                              |
| 2    | tm_mday  | 1 到 31                              |
| 3    | tm_hour  | 0 到 23                              |
| 4    | tm_min   | 0 到 59                              |
| 5    | tm_sec   | 0 到 61 (60或61 是闰秒)              |
| 6    | tm_wday  | 0到6 (0是周一)                       |
| 7    | tm_yday  | 1 到 366(儒略历)                     |
| 8    | tm_isdst | -1, 0, 1, -1是决定是否为夏令时的旗帜 |

------

#### 获取当前时间

从返回浮点数的时间戳方式向时间元组转换，只要将浮点数传递给如localtime之类的函数。

------

#### 获取格式化的时间

你可以根据需求选取各种格式，但是最简单的获取可读的时间模式的函数是asctime():

#### 格式化日期

我们可以使用 time 模块的 strftime 方法来格式化日期，：

```
time.strftime(format[, t])
```

- %y 两位数的年份表示（00-99）
- %Y 四位数的年份表示（000-9999）
- %m 月份（01-12）
- %d 月内中的一天（0-31）
- %H 24小时制小时数（0-23）
- %I 12小时制小时数（01-12）
- %M 分钟数（00-59）
- %S 秒（00-59）
- %a 本地简化星期名称
- %A 本地完整星期名称
- %b 本地简化的月份名称
- %B 本地完整的月份名称
- %c 本地相应的日期表示和时间表示
- %j 年内的一天（001-366）
- %p 本地A.M.或P.M.的等价符
- %U 一年中的星期数（00-53）星期天为星期的开始
- %w 星期（0-6），星期天为星期的开始
- %W 一年中的星期数（00-53）星期一为星期的开始
- %x 本地相应的日期表示
- %X 本地相应的时间表示
- %Z 当前时区的名称
- %% %号本身

------

#### Time 模块

Time 模块包含了以下内置函数，既有时间处理的，也有转换时间格式的：

| 序号 | 函数及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | [time.altzone](https://www.runoob.com/python/att-time-altzone.html) 返回格林威治西部的夏令时地区的偏移秒数。如果该地区在格林威治东部会返回负值（如西欧，包括英国）。对夏令时启用地区才能使用。 |
| 2    | [time.asctime([tupletime\])](https://www.runoob.com/python/att-time-asctime.html) 接受时间元组并返回一个可读的形式为"Tue Dec 11 18:07:14 2008"（2008年12月11日 周二18时07分14秒）的24个字符的字符串。 |
| 3    | [time.clock( )](https://www.runoob.com/python/att-time-clock.html) 用以浮点数计算的秒数返回当前的CPU时间。用来衡量不同程序的耗时，比time.time()更有用。 |
| 4    | [time.ctime([secs\])](https://www.runoob.com/python/att-time-ctime.html) 作用相当于asctime(localtime(secs))，未给参数相当于asctime() |
| 5    | [time.gmtime([secs\])](https://www.runoob.com/python/att-time-gmtime.html) 接收时间戳（1970纪元后经过的浮点秒数）并返回格林威治天文时间下的时间元组t。注：t.tm_isdst始终为0 |
| 6    | [time.localtime([secs\])](https://www.runoob.com/python/att-time-localtime.html) 接收时间戳（1970纪元后经过的浮点秒数）并返回当地时间下的时间元组t（t.tm_isdst可取0或1，取决于当地当时是不是夏令时）。 |
| 7    | [time.mktime(tupletime)](https://www.runoob.com/python/att-time-mktime.html) 接受时间元组并返回时间戳（1970纪元后经过的浮点秒数）。 |
| 8    | [time.sleep(secs)](https://www.runoob.com/python/att-time-sleep.html) 推迟调用线程的运行，secs指秒数。 |
| 9    | [time.strftime(fmt[,tupletime\])](https://www.runoob.com/python/att-time-strftime.html) 接收以时间元组，并返回以可读字符串表示的当地时间，格式由fmt决定。 |
| 10   | [time.strptime(str,fmt='%a %b %d %H:%M:%S %Y')](https://www.runoob.com/python/att-time-strptime.html) 根据fmt的格式把一个时间字符串解析为时间元组。 |
| 11   | [time.time( )](https://www.runoob.com/python/att-time-time.html) 返回当前时间的时间戳（1970纪元后经过的浮点秒数）。 |
| 12   | [time.tzset()](https://www.runoob.com/python/att-time-tzset.html) 根据环境变量TZ重新初始化时间相关设置。 |

Time模块包含了以下2个非常重要的属性：

| 序号 | 属性及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | **time.timezone** 属性time.timezone是当地时区（未启动夏令时）距离格林威治的偏移秒数（>0，美洲;<=0大部分欧洲，亚洲，非洲）。 |
| 2    | **time.tzname** 属性time.tzname包含一对根据情况的不同而不同的字符串，分别是带夏令时的本地时区名称，和不带的。 |



------

#### 日历（Calendar）模块

此模块的函数都是日历相关的，例如打印某月的字符月历。

星期一是默认的每周第一天，星期天是默认的最后一天。更改设置需调用calendar.setfirstweekday()函数。模块包含了以下内置函数：

| 序号 | 函数及描述                                                   |
| :--- | :----------------------------------------------------------- |
| 1    | **calendar.calendar(year,w=2,l=1,c=6)** 返回一个多行字符串格式的year年年历，3个月一行，间隔距离为c。 每日宽度间隔为w字符。每行长度为21* W+18+2* C。l是每星期行数。 |
| 2    | **calendar.firstweekday( )** 返回当前每周起始日期的设置。默认情况下，首次载入 calendar 模块时返回 0，即星期一。 |
| 3    | **calendar.isleap(year)** 是闰年返回 True，否则为 False。`>>> import calendar >>> print(calendar.isleap(2000)) True >>> print(calendar.isleap(1900)) False` |
| 4    | **calendar.leapdays(y1,y2)** 返回在Y1，Y2两年之间的闰年总数。 |
| 5    | **calendar.month(year,month,w=2,l=1)** 返回一个多行字符串格式的year年month月日历，两行标题，一周一行。每日宽度间隔为w字符。每行的长度为7* w+6。l是每星期的行数。 |
| 6    | **calendar.monthcalendar(year,month)** 返回一个整数的单层嵌套列表。每个子列表装载代表一个星期的整数。Year年month月外的日期都设为0;范围内的日子都由该月第几日表示，从1开始。 |
| 7    | **calendar.monthrange(year,month)** 返回两个整数。第一个是该月的星期几的日期码，第二个是该月的日期码。日从0（星期一）到6（星期日）;月从1到12。 |
| 8    | **calendar.prcal(year,w=2,l=1,c=6)** 相当于 **print calendar.calendar(year,w=2,l=1,c=6)**。 |
| 9    | **calendar.prmonth(year,month,w=2,l=1)** 相当于 **print calendar.month(year,month,w=2,l=1)** 。 |
| 10   | **calendar.setfirstweekday(weekday)** 设置每周的起始日期码。0（星期一）到6（星期日）。 |
| 11   | **calendar.timegm(tupletime)** 和time.gmtime相反：接受一个时间元组形式，返回该时刻的时间戳（1970纪元后经过的浮点秒数）。 |
| 12   | **calendar.weekday(year,month,day)** 返回给定日期的日期码。0（星期一）到6（星期日）。月份为 1（一月） 到 12（12月）。 |


### 函数相关知识
##### 作用域的知识
- `L(Local)`  局部作用域
- `E(Enclosing)`  闭包函数外的函数中
- `G(Global)`   全局作用域
- `B(Build-in)`  内建作用域
变量的查找顺序：`L -> E -> G -> B`
会影响 函数/变量 的作用域的有
- 函数：`def `或者`lambda`
- 类：`class`
- 关键字：`global`和`nonlocal`
- 文件：*py
- 推导式：[]{}()
********

##### 变量相关的内建函数
- `globals()`：以dict形式存储所有全局变量
- `locals()`：以dict形式存储所有局部变量
********

##### 函数定义

********

##### 函数参数

********

##### 函数闭包
闭包满足条件：
1. 必须是嵌套函数，即函数内部定义了另外一个函数
2. 内部函数必须调用外部函数变量
3. 外部函数返回了内部函数或者外部函数调用了内部函数
查看是否是闭包：`__closure__`
内部函数的调用：`outer = outer(),outer()`
*******
==注意：==
内函数不能直接被调用。
闭包中内部函数引用的变量不会因为外部函数的结束而释放，如果外函数在结束的时候发现自己的临时变量再将来会被内部函数用到，就把变量绑定给了内部函数，自己再结束，这个变量而是一直在内存中，直到内部函数结束。

*********
内部函数想要修改外部函数变量，有两种方法：
1. 将外部函数的变量在内部函数声明位非局部变量，`nonlocal variable_name`
2. 将闭包变量改成可变数据类型进行改变，如列表。
*********
==注意：==

1. `nonlocal`关键字只能用与嵌套函数中，外部函数定义了相关的变量。
2. 外部函数调用一次返回了内函数，实际闭包变量只有一份，以后每次调用内函数，都是用同一份闭包变量，一旦闭包变量内部修改了外部变量的值，则这个变量值就已经修改了，不是最初值。
********

##### 柯里化和偏函数

**********

##### 函数修饰器
装饰器本质是一个函数，使用了闭包的特性，可以让其他函数不需要做任何代码变动的情况下增加额外的功能，装饰器的返回值也是一个函数对象。装饰器的定义就是将外部使用装饰器的函数作为参数，然后装饰器内部在定义需要增加的功能内函数。
```python
def timer(func):
	def inner():
		time_start = time.time()
		func(*args, **kwargs)
		end_time = time.time()
		print(f'函数运行了{time_end - time_start')
	return inner
@timer
func(*args, **kwargs) # 这时调用timer装饰器即可直接得出函数运行的时间
# 如此定义完之后再使用函数即可
```
********
装饰器类别：
1. 装饰器不带参数：如上一段代码中的内容
2. 带参数：
```python
def flog(name):
	def timer(func):
        def inner():
        	print(f'调用{name}模块进行装饰器演示')
            time_start = time.time()
            func(*args, **kwargs)
            end_time = time.time()
            print(f'函数运行了{time_end - time_start')
        return inner
    return timer
@flog("模块1")
func(*args, **kwargs)
```
*******

### python虚拟环境
##### 虚拟环境的管理
管理好用的是`virtualenv`和`virtualenvwrapper-win`和Linux下是`virtualenvwrapper`
###### `virtualenv`和`wrapper`的使用方法
- `virtualenv`使用：
1. 创建：`virtualenv env_name`或`virtualenv -p /usr/bin/python-version env_name`Linux可以指定版本并写入环境变量`echo "export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python-version">>~/.bashrc
2. 进入：`cd source_name\bin` then`activate`,退出：`deactivate`
3. `cd Env_name`then `rm-rf virtual_env_name`
4. 使用全局一样的包：
导出依赖包：`pip freeze > requirements.txt`
安装依赖包：`pip install -r requirements.txt
- `virtualenvwrapper`使用
配置：`find / -name virtualenvwrapper.sh然后在~/.bashrc新增：`
```
export WORK_HOME=$HOME/.virtualenvs
export PROJECT_HOME=$HOME/workpace
export VIRTUALENVWRAPPER_SCRIPT=/usr/binvirtuawrapper.sh
source /usr/bin/virtualwrapper.sh
```
*********
基本语法
`mkvirtualenv [-a project_path] [-i package] [-r requirements_file][virtualenv options] env_name`

1. 创建`mkvrtualenv env_name`
2. 进入：`workon env_name`退出：`deactivate`
3. 列出所有虚拟环境：`workon`or`lsvirtualenv`
4. 删除：`rmvirtualenv env_name`切换`workon env_name`即可
5. 其他命令
```
virtualenvwrapper # 列出帮助文档
cpvirtualenv env_name [target_env] # 拷贝虚拟环境
allvirtualenv pip install -U pop # 在所有虚拟环境执行命令
wipeenv # 删除当前环境所有第三方包 
cdsitepavkages # 进入当前虚拟环境目录
cdvirtualenv # 进入当前环境的site-package目录
lssitepavkage # 显示site-package 内容
```
###### 实战使用
- 交互式环境中：`workon env_name `然后python命令进入虚拟环境的python
- 工程项目：哟=有一个入口环境，其首行可以指定python解释器，想要在想要的虚拟环境中使用，改变该头部信息即可。`#!/root/.virtualenvs/env_name/bin/python`
然后命令行运行，运行前添加权限`chomd +x file_name.py`then`./file_name.py`
- pycharm中：设置里面设定文件的python解释器即可
*******

### 类相关知识

##### 类方法的强制重写和禁止重写
01. 强制重写
需求：父类的一个方法，需要强制子类重写
实现方法：
- 把父类变为抽象基类，然后给指定的方法加上装饰器`@abc.abstractmethod`
- 指定方法抛出`NotImplementedError`异常
第一种方法代码实现：
```python
# python2
import abc

class Animal(object):
	__metaclass__ = abc.ABCMeta
	
	@abc.abstractmethod
	def speak(self):
		pass
# python3 
class Animal(metaclass=abc.ABCMeta):
	@abc.abstractmethod
	def speak(self):
		pass

class Dog(Animal):
	def speak()
# 如果子类不重写，在子类的实例化对象的时候就会报错Typeerror,不需要到调用方法才抛出
```
第二种方法：
```python
class Animal():
	def speak(self):
		raise NotImplemented

class Dog(Animal):
	def speak(self):
		print("ss")
# 如果不重写写了抛出异常的方法，会在子类的方法调用时抛出异常
```
*********

02. 禁止重写
说法不太准确，实际可重写，只是无法生效
```Python
# 常规例子
class Base:
	def go(self):
		print("base")
	def run(self):
		self.go()
cass Extend(Base):
	def go(self):
		print("Extend")
# 这时实例化对象并调用run方法返回的分别时base和extend
class Base:
	def __go(self):
		print("base")
	def run(self):
		self.__go()
class Extend(Base):
	def __go(self):
		peint("extend")
# 这时实例化对象并调用run方法全部返回base
# 两者区别在于一个公开函数一个是私有函数
# 私有函数作用范围仅在当前类，其表象上可以被重写，实际并无重写的效果
```
******
##### 类的多继承与Mixin设计模式
01. 认识Mixin模式



02. 不使用Mixin的弊端


### python调试
##### pdb调试
python3.7以后设置断点直接使用`breakpoint()`方法即可，之前的需要自己导入`pdb`，打断点调用`set_trace()`，这时就可以使用pdb调试了
在命令行下python运行就可以debug了

这时出现的第一行：告诉我们打断点的位置
第二行：执行执行的暂停位置
第三行：可以输入相关的debug命令了

**pdb常用命令**：利用help可以查看相关的帮助信息
- p + 参数名 ：查看参数的值
- n ：单步跳过
- s ：单步进入
- c ：继续执行
- w ：显示上下文信息
- a ：查看函数的参数列表
- ll ：列出当前的源码
- b ：设置断点
- q ：推出debug
在debug的过程中可以修改参数值继续进行调试

还可以不设置断点直接命令行的方式执行，直接在第一行打断点
`python3 -m pdb xxx.py`
*******

### python对象
##### 不可更改对象和可更改对象
如何判别：
1. 利用`id()`函数查看对象或变量或参数进行某种操作前后的值，如果不相等，则为不可更改对象，如果相等，则为可更改对象。
2. 利用`hash()`函数查看，如果不报错，证明可被hash，即不可更改，反之，不可hash，可更改。

一般创建一个变量就创建了一个`PyObject`，可更改对象起始时一直再这个对象上进行操作，所以器指向的id是不变的，不可更改对象在每一次操作都会重新创建一个`PyObject`，因此id是变化的。数组字符串可更改，数字不可更改
**********
##### 函数默认参数
函数的默认参数值只能被初始化一次，如果使用的是可更改对象为参数值，那么每次修改的值会椅子保留
**********
##### print()函数的细节
python3中，print()是函数而不是语句，因此函数中的参数需要在调用函数前被估值，因此里面的参数都是先得到值后，再传入函数中打印。而且每个函数的指向id都是不变的。如果定义了多个变量，但是都传入同一函数同一参数，则这两个变量的id指向一致
***********
##### None类型
None和整数、浮点数、布尔一样是一种不可变参数，类型是Nonetype，所以一般默认参数值选择使用None。

******

### python异常相关知识
##### 不要滥用`try:  except`:语句
1. 如果滥用异常捕获语句，可能有时候会隐藏掉异常信息难以发现错误所在
2. 在程序开发初期，不用`try:  except: `语句，需要让错误暴露出来，通过报错信息，可以明确知道错误所在。有时候还需要主动抛出异常。第三方库最好多主动抛出异常，这样才能让别人知道错误所在，有时候甚至可以使用python的断言语句，`assert`。
3. 一般异常捕获语句最好是用来捕获具体的异常而不是所有异常，这样才能清晰的知道是什么异常，才能更好的分析原因。
4. 又是甚至可以利用自带的`traceback`模块，强行打印报错信息。
```python
# traceback的用法
import traceback
try:
	1 + "a"
except:
	print(traceback.format_exc())
```
==**总结：**==
`try: except`语句可以让代码看起来没有问题，但有时也会掩盖你想查找的问题，所有尽量删除不需要的语句，拥抱异常，让代码的纠正查错更加方便，让代码不带着问题继续运行。
*********

### python文件操作
##### 方法函数
1. `os.getcwd()`获取当前运行路径
2. `os.path.join()`连接路径 `# 是mac和Linux的路径符，windows是\`
3. `os.listdir()`和`os.scandir()`列出路径下文件夹中的所有文件夹和文件，后者返回的是一个迭代器对象
4. `os.isdir()`判断路径是否为文件夹
5. `os.walk()`遍历路径下的文件夹中的文件并返回列表
6. `os.startwith()`和`os.endwith()`是否以传入字符开头或者结尾
7. `glob`模块`glob.glob()`进行文件匹配,`*匹配所有,?匹配单个字符,[]和[!]匹配在和任何不在方括号内的字符`
8. `os.scandir()`返回的文件可以使用`stat()`方法查看文件的信息
- `st_size`：文件体积大小（单位bytes）
- `st_atime`：文件的最近访问时间
- `st_mtime`：文件的最近修改时间
- `st_ctime`和`st_birthtime`：windows创建时间和macLinux创建时间
9. `os.mkdir()`：创建文件夹文件夹已经存在会报错
10. `shutil`模块的复制：`shutil.copy('copy_file','copy_path')`复制文件，`shutil.copytree()`参数同上，复制文件夹
11. `shutil.move('file','move_peth')`移动：文件文件夹都是这个
12. `os.rename('old_file','new_file')`重命名：文件文件夹都可
13. `os.remove('file_name')`删除文件：只删除文件
14. `shutil.rmtree()`删除文件夹
15. `zipfile`模块：创建和解压压缩包
- `zipfile.ZipFile('file','r')`读取：可用上下文管理器读取，对象的`namelist()`返回压缩包里的文件，`obj.getinfo('file_name')`查看文件的信息
- `extract('解压文件名','解压路径')`和`extractall('解压路径')解压
- `zipfile.ZipFile('file_name','w')`创建压缩包：利用`write('file')`方法写入'w'改为'a'往已有压缩包添加文件
********

### python的random模块
1. `random.random()`是返回零到一的随机浮点数
2. `random.uniform(a,b)`返回a到b的随机浮点数
3. `random.randint(start,stop,step)`返回随机整数,在指定范围和步长的范围选
4. `random.randrange()`和上面一样
5. `random.choice()`从序列中选取一个随机元素,sequece参数指tuple,list字符串
6. `random.shuffle()`用于将列表元素打乱
7. `random.sample(seq,k)`指定序列许纳泽指定长度片段,不修改原序列

### python提升性能的方法
1. **使用局部变量**：尽量使用局部变量代替全局变量，便于维护，提高性能并节省内存。还可以使用局部变量来替换模块中的命名空间的变量，这样可以提升程序性能，局部变量的查找顺序更快，另外使用阶段的标识符变量来取代冗长的模块变量。提升可读性。
2. **减少函数的调用次数**：对象类型判断的时候使用isinstance()最优，id()次之，type()较次。对于需要重复操作的内容不要放在循坏条件中去，尽量以变量或者其他方便形式，减少重复内容的计算。如需使用某些模块下的某个函数或者对象，使用`from x import y`
3. **采用映射关系来代替条件查找**：映射（如dict等）的搜索速度远快于条件语句
4. **直接迭代序列元素**：不利用索引遍历，直接遍历
5. **生成器表达式代替列表表达式**：生成器表达式返回的是生成器，更节省内存`i = (i*2 for i in range(10)), j = [i*2 for i in range(10)]`
6. **先编译后调用**：使用`eval()`和`exec()`函数执行代码时，最好调用代码对象，即经过编译函数`compile()`编译的对象，可避免重复编译提高性能，正则表达式也是这样类似。
7. **模块编程习惯**：模块中最高级别的代码（没有缩进的代码）会在模块导入时执行，尽量让模块的功能代码放入到函数内部中去，还可使用`if __name__ == '__main'避免调用模块执行测试代码

### python生成器和协程
#### 一些概念
1. 可迭代：是指可以使用for语句获取内部内容的对象，元组，列表，字典，字符串都是，`dir()`查看，内部有`_iter_`说明时可迭代，没有也不一定不可迭代，实现了`_getitem_`也是可迭代，最好通过for循环或者iter()真实运行
2. 迭代器：使用`iter()`生成的对象，其实际就是在内部实现了`__next_`魔法方法。可使用isinstance()结合Iterable，Iterator和Generator来判断相关内容。迭代器有个函数`next()`可以获取其子元素。
3. 生成器：yield关键字生成的内容属于生成器
迭代器在可迭代的基础实现了next()方法，生成器在迭代器上实现了yield
#### yield生成器
生成器是为了实现一个在计算下一个值时不需要浪费空间的结构
yield：相当于函数中的return，在每一次next()或者for迭代时，将这个yield的值返回，并在这里阻塞，等待下一次的调用。
##### 生成器创建：
1. 生成器表达式`i = (i**2 for i in range(10))`
2. 实现了`yield`的函数
==优点==：生成器是在元素需要的时候才临时生成，节省时间和内存，迭代器和可迭代对象，将所有值都放在内存空间不节省内存。
##### 生成器激活运行：
1. `next()`方法
2. 使用生成器的`send()`方法：`generator.send(None)`

##### 生成器的执行状态：
1. `GEN_CREATED`等待开始执行
2. `GEN_RUNNING`解释器正在执行（只有在多线程应用中才能看到这个状态）
3. `GEN_SUNPENDED`在yield表达式出暂停
4. `GEN_CLOSED`执行结束
使用getgenerotorstate()方法传入generator查看状态，生成器的close()方法可以手动关闭结束生成器
##### 生成器的异常处理：
在生成器的工作过程中，若生成器不满足元素的条件，会或者应该抛出异常
生成器创建自动实现了Stopiteration异常。创建生成器时，也应该在不满足生成器条件时主动抛出异常
##### 生成器过渡到协程：
生成器其实为我们引入了暂停函数执行的功能，在暂停之后，可以通过生成器的send()函数发送东西给生成器。这催生了协程的诞生。协程通过yield暂停生成器，可以将协程的执行流程交给其他子程序，从而实现不同子程序的交替进行。
```python
def jump_range(N):
	index = 0
	while index < N:
		jump = yield index
		if jump is None:
			jump = 1
		index += jump
if __name__ == '__main__':
	gen = jump_range(10)
	print(next(gen))  # 0
	print(gen.send(2))  # 2
	print(next(gen))  # gen.send(None) == next(gen)  # 3
	print(gen.send(-1))  # 2
# jump = yield index 分两部分，yield index将index至返回给外部调用函数，jump = yield可以接收外部程序通过send()发送的值并赋值给jump
```

##### 为什么要使用协程：
爬虫时遇到请求网页这种需要耗时的请求，可以先暂停一下去处理其他的事情，过一段时间再回来处理，这种就可以节省等待的时间。而yield正好提供了这样的一个暂停功能
使用常规的同步编程要实现异步并发效果不是很理想。由于线程锁的机制存在，加锁解锁切换线程，极大的降低了并发性能
特点：
1. 协程是在单线程下实现的任务切换
2. 利用同步的方式实现异步
3. 不需要使用锁，提升了并发性能
##### yield from：
简单用法：拼接可迭代对象yield from后面需要加的是可迭代对象，可以是普通可迭代对象，也可以是迭代器和生成器
```python
astr = 'hello'
alist = [1, 2, 3, 4]
atuple = (5, 6, 7, 8)
adict = {'a':1, 'b':2, 'c':3}
def gen_iter(*args, **kw):
	for item in args:
		for i in item:
			yield i
def gen_iter_from(*args, **kw):
	for item in args:
		yield from item
```

复杂应用：生成器的嵌套，yield from后面加生成器就实现了生成器的嵌套，使用yield from可以避免我们自己处理其中遇到的意想不到的异常。
一些概念：

1. 调用方：调用委派生成器的客户端代码
2. 委托生成器：包含yield from表达式的生成器函数
3. 子生成器：yield from后面加的生成器函数
```python
# 子生成器
def gen_average():
	total = 0
	count = 0
	average = 0
	while True:
		new_num = yield average
		count += 1
		total += new_num
		average = tatal / count

# 委托生成器
def gen_average_client():
	while True:
		yield from gen_average

# 调用方
def main():
	calc_average = gen_average_client()
	next(calc_average)    # 预激生成器
	print(calc_average.send(10))  # 10
	print(calc_average.send(20))  # 15
	print(calc_average.send(30))  # 30
```
委托生成器的作用：在生成器和调用方之间生成一个双向通道，调用方可以通过send发送消息给子生成器，子生成器的yield的值可以返回给调用方
```python
# 子生成器
def gen_average():
	total = 0
	count = 0
	average = 0
	while True:
		new_num = yield average
		if new_num is None:
			break
		count += 1
		total += new_num
		average = tatal / count
	return count, total, average

# 委托生成器
def gen_average_client():
	while True:
		yield from gen_average

# 调用方
def main():
	calc_average = gen_average_client()
	next(calc_average)    # 预激生成器
	print(calc_average.send(10))  # 10
	print(calc_average.send(20))  # 15
	print(calc_average.send(30))  # 30
	print(calc_average.send(None))  # 结束生成器协程
```
为什么用yield from：
用它可以帮助我们处理异常而不需要我们自己去书写异常代码块
1. 迭代器（子生成器）生成的值直接返回给调用者
2. 任何使用send()方法发送给委派生成器的值直接传递给子生成器。send()值为None，调用next，不为None，调用send方法如果迭代器调用产生StopIteration异常，委派生成器继续执行yield from后面的语句，产生其他异常，则都传递给委派生成器

### python装饰器
```python
def hello_decorator(func):
	def wrapper(*args, **kw):
		return func()
	return wrapper
@hello_decorator
def hello():
	print('hello decorator')
```
1. 日志装饰器
```python
import time
def logger_deco(func):
	def wrapper(*args, **kw):
		print('开始运行，time.strftime("%Y-%m-%d %H:%M:S", time.localtime(time.time()))')
		func()
		print('开始运行，time.strftime("%Y-%m-%d %H:%M:S", time.localtime(time.time()))')
	return wrapper
@timer_deco
def calculate_1_plus2():
	print(1 + 2)
	time.sleep(0.5)
```
2. 运行时间装饰器
```python
import time
def timer_deco(func):
	def wrapper(*args, **kw):
		start_time = time.time()
		func()
		end_time = time.time()
		print(f'END:{start_time - end_time}s')
	return wrapper
@timer_deco
def calculate_1_plus2():
	print(1 + 2)
	time.sleep(0.5)
```
3. 带参数的装饰器
```python
def args_decorator(nationality='China'):
	def inner_fun(func):
		def wrapper(*args, **kw):
			if nationality is 'China':
				print(f'你好，我是{func()}')
			elif nationality is 'american':
				print(f'hello my name is {func()}}')
		return wrapper
	return inner_fun

@args_decorator('China')
def xiaoming():
	return '小明'
xiaoming()
```
4. 高阶：不带参数的装饰器类。基于类的装饰器，必须实现`__call__`和`__init__`前者实现装饰的逻辑，后者接受被装饰的函数
```python
class logger(object):
	def __int__(self, func):
		self.func = func
	
	def __call__(self):
		print('我要被调用了')
		self.func()
		print('我被调用完毕了')
		return self.func()

@logger
def hello():
	print('hello world')
hello()
```
5. 带参数的装饰器类
```python
class logger(object):
	def __init__(self, level, func):
		self.level = level
		self.func = func
	def __call__(self):
		print(f'{self.level}: {self.func()}')

@logger('Info')
def hello():
	return 'hello world'
hello()
```
6. 使用偏函数和类实现装饰器：大部分装饰器是通过函数和闭包来创建的，这并不是位移途径。python某个对象能否通过装饰器形式使用只有一个要求，decorator必须是一个可被调用的对象，除了函数外，类只要实现`__call__`也是callable对象，还有偏函数也是callable对象
```python
import functools
class DelayFunc:
	def __init__(self, duration, func):
		self.func = func
		self.duration = duration
	
	def __call__(self, *args, **kwargs):
	print(f'wait for {self.duration}s')
		time.sleep(self.duration)
		return self.func(*args, **kwargs)

def delay(duration):
	return functools.partial(DelayFunc, duration)
@ delay(2)
def hello():
	print('hello world')
	return 5
hello()
```

### python注释类型标注
类型注解的语法：
- 在生声明变量的时候，在变量的后面添加冒号，后面加上变量类型，int,str,list,tuple,float, dict,
- 声明方法函数的返回值的时候，在方法的后面加个箭头，后面加上返回值的类型
- pep8的语法规定在变量后面紧跟冒号再接空格接变量类型，方法注解箭头左边时方法定义，右边是返回值类型，左右两边都要有空格，这种声明只是一个变量注释，不会对函数的运行产生影响，只是一种提示，在pycharm会有提示
```python
a: int = 2
b: str = 'hello'
def func(a: int, b: str) -> float:
	print(a, b)
	return 1.5
```
尽管这种注释已经可以告诉一些变量的类型了，但对于列表字典，并不能指导其内部内容的类型，这就需要用到`typing`模块了，它提供非常强的类型支持，如`List[str]`,`Tuple[int, int, int]`,`Dict[str, bool]`表示为字符串类型的列表，整型的元组和键为str值为布尔类型的字典，`typing`已经加入标准库
```python
from typing import List, Turple, Dict  # 引入即可用上述的类型注解
# 列表List
var: List[int or float] = [1, 1.5]
var: List[List[int]] = [[1],[1,2]]  # 也支持嵌套声明
# 元组Tuple和NamedTuple，推荐使用attrs而不是namedtuple
person: Tuple[str, int, float] = ('li', 20, 1.5)  # 也可类型嵌套
# Dict、Mapping，MutableMapping(mapping子类，很多库用这个)
# Dict推荐用于返回值注解，Mapping用于参数注解，两者用法一致，Dict[str, bool]
def recognize(rect: Mapping[str, float]) => Dict[str, float]:
	return {'dd': 1.2}
# Set 和AbsractSet，前者返回值注解，后者参数注解
# Sequence在不需要严格区分元组列表用这个
# NoReturn没有返回结果用这个
# Any代表所有类型，用这个和无声明是相等的
# TypeVar 借助它来定义自定义兼容类型的变量，比如有的变量可以是多个类型都符合要求，可以使用这个类型来定义
Height = TypeVar('Height', int, float, None) # 定义一个身高类型，整数浮点数None都可以
def return_height() -> Height:
	return 1.83
# NewType可以借助这个来定义具有特殊含义的类型，例如tuple，我们可以定义一个person的tuple例子
Person = NewType('Person', Tuple[str, int, float])
person = Person(('li', 18, 18.5)) # 这个person就和正常的tuple一样
# Callable可调用类型，通常注解方法声明时需使用Callable[[arg1type, arg2type], returntype]类似注解，将参数和返回值注释出来
# Union联合类型Union[X, Y],要么X要么Y，联合的联合类型就是展平的类型，多余参数会跳过，一个参数即等同于该参数
# Optional可以为空或者声明的类型，Optional[int]等同于Union[int, None]
# 为None不代表可以不传递，只是说可传为None
# Generate生成器的类型声明Generate[yieldType, sendType, ReturnType]
# yieldType是指yield紧跟的变量类型，snedType返回的结果类型，第三个是最后返回的类型
# 很多情况不需要设置snedType和ReturnType，可以将其设置为None
```
********
### python代码退出强制执行一段代码
利用python自带模块`atexit`，使用方法：
```python
import atexit

@atexit.regsiter
def clean():
	print("我是最后代码结束执行的代码")

start()
execute_fun()
# 不需要显示调用clean()函数都能再结束的时候自动运行
```
`atexit`模块的使用要点：
- 可以定义多个推出函数，按照注册时间从晚到早依次执行。
- 如果函数有参数，可以不利用装饰器。直接调用`atexit.register(fun_name, arg1, arg2, argn)`
- 如果程序被你没有处理过的系统信号杀死的，那么注册的函数无法正常执行
- 如果发生了严重的python内部错误，无法正常执行
- 如果手动调用了`os._exit()`，注册的函数无法正常执行

********

### python使用技巧

1. 查看源代码，使用`inspect`模块
`inspect.getsource(func_name)`或者传入参数为模块名

2. 异常关联上下文
处理异常时，处理不当或者其他问题，再次抛出一个异常时，往外抛出的异常会携带原始异常
一般处理时：
```python
try：
	print(1 / 0)
except Exception as exc:
	raise RunError('something bad happend') from exc
	raise RunError('something bad happend').with_traceback(exc)
	raise RunError('something bad happend') from None  # 彻底关闭原始已成信息
```
3. 查看包搜索路径：
`import sys, from pprint import pprint, pprint(sys.path)`
`python -c 'print('\n'.join(__import__('sys').path))'命令行查看
`python -m site`另外一种更方便的命令行查看，包含了用户环境的目录
4. 嵌套for循环的简洁写法
```python
from itertools import product
list1 = range(1, 3)
list2 = range(4, 6)
list3 = range(7, 9)
for item1, item2, item3 in product(list1, list2, list3):
	print(item1 + item2 + item3)
```
5. 使用`print`输出日志`with open('test.log', 'w') as f:print('hello', file=file, flush=True)`
6. 计算函数运行时间：`import timeit, timeit.timeit(func_name, number=5)`后面参数为函数的运行次数
7. 利用python自带的缓存机制提升效率。缓存就是将定量数据加以保存，一边后续获取处理的处理方式。如果一个数据需要多次使用，每次都重新生成会耗费大量的时间，如果利用缓存机制就可以减少运算时间提高效率。
```python
from functools import lru_cache
# 装饰器的参数说明，maxsize：最多可以缓存多少个函数的调用结果，None为无限制，设置为2的幂次性能最佳，typed是否不同类型参数调用分别缓存
@lru_cache(maxsize=None, typed=False)
def func(x, y):
	return x + y
# 此时如果调用两次func(1, 2)则后一次不会调用函数，直接返回结果
```
8. `python`实现`golang`的延迟调用,`golang`通过`defer`关键字定义延迟调用
```python
import contextlib
def callback():
	print('B')
with contextlib.ExitStack() as stack:
	stack,callback(callback)
	print('A')  # 此时输出为AB
```
9. 读取文件时，最好指定每次读取的内容大小
```python
def read_chunk_from_file(filename, block_size=1024 * 8):
with open(filename, 'r') as fp:
	while True:
		chunk = fp.read(block_size)
		if not chunk:
			break
		yield chunk
# 借助偏函数优化一下
from functools import partial
def read_chunk_from_file(filename, block_size):
	with open(filename, 'r') as fp:
		for chunk in iter(partial(fp.read, block_size), ''):
			yield chunk
```
10. 正则表达式中的`re.sub(pattern, repl, string)`其中的第二个参数可以是一个函数对象，函数传入值是匹配的对象，函数的返回值就是匹配的字符串替换字符。该参数可以利用于需要对不同的匹配对象进行相应的操作的时候
11. 当if else层数过多时可以考虑使用`python`的字典来进行一定的美化:
```python
def hello():
    print('hello')

def requests_url(url):
    print(url)
# 字典定义的函数可以单独放在一个文件，再进行调用就好
dict_select = {'hello':hello, 'world':requests_url}
dict_select.get('hello')()
dict_select.get('world')('sssssss') # 字典函数可以传参数，这样结合会有不错的效果
```
12. 模块文件中的`__all__ = ['func_name', 'class_name']`有这个东西之后智慧影响`from module import *`的行为,不会影响`from module import fun_name`之类的操作
13. 生成器表达式：`generator_exp = (i **2 for i in range(5))`生成器表达式是对内存空间的优化，比列表解析式稍慢，但是较节省空间。生成器只能遍历一次。
14. 列表解析式：`list_exp = [i **2 for i in range(5)]`
15. 字典解析式：`dict_exp = {i:i ** 2 for i in range(5)}`

*********
#### `pythonic`的代码实例

01. 变量交换
`a,b = b,a`
02. 列表推导式
`a = [i + 3 for i in range(10)]`
03. 单行表达式
```
# bad示例
print("1");print(2)
if <complex comparison> and <another complex comparison>
# good示例
print(1)
print(2)
a = <complex comparison>
b = <another complex comparison>
if a and b
```
04. 利用`enumerate`带索引遍历
05. 序列解包
```
a, *rest = [1,2,3] # a=1,rest=[2,3]
a, *rest, c =[1,2,3,4] # a=1,c=4,rest=[2,3]
```
06. 利用`join`进行字符串的拼接
07. 真假判断：
`if attr: or if not attr: or if attr is None:`
08. 访问字典元素最安全的是利用`get()`方法,参数为键名和找不到时返回的值，或者使用if判断键名是否在字典中
09. 代码续行最好使用括号括起来进行续行
10. 显示代码：代码尽量简明易看懂
11. 使用占位符：
```
filename = 'file.txt'
basename, _, ext = filename.rpartition('.')
```
12. 链式比较：
```
# bad示例
if a > 18 and a < 20:
# good示例
if 18 < a < 20:  # 链式比较时先前两个比较然后后两个比较，再一起比较
```
13. 等号后边多个值可以打包成元组`t = "hello", 'world', 'nice tool'`变量t就变为元组了

### 遇见编译错误的需要visual c++14.0的情况
安装visualstudio2015，或者安装mingw
#### GCC - MinGW-w64 (x86, x64)

遇到报错版本号的问题，修改cygwinccompiler文件的内容
```
elif msc_ver == '1914':   # 按照相应的版本写个elif就好
# VS2010 / MSVC 10.0
	return ['msvcr100']
```
[MinGW-w64](http://mingw-w64.org/) is an alternative C/C++ compiler that works with all Python versions up to 3.4.

- Install *[Win-builds](http://win-builds.org/doku.php/download_and_installation_from_windows)* into *C:\MinGW_w64*.
- Open *Win-builds*, switch to *install* at least *binutils*, *gcc*, *gcc-g++*, *getext*, *mingw-w64*, *win-iconv*, *winpthreads*, *zlib*, and click *Process*.
- Add *C:\MinGW_w64\bin* to the *PATH* environment variable.
- Create a *distutils.cfg* file with the following contents in the folder *\Lib\distutils* in Python install directory :

[Toggle line numbers](https://wiki.python.org/moin/WindowsCompilers#)

```
[build]
compiler=mingw32

[build_ext]
compiler=mingw32
```

#### GCC - MinGW (x86)
[MinGW](http://www.mingw.org/) is an alternative C/C++ compiler that works with all Python versions up to 3.4.

- Install *[Minimalist GNU For Windows](http://sourceforge.net/projects/mingw/files/)* into *C:\MinGW*.
- Open *MinGW Installation Manager*, check *mingw32-base* and *mingw32-gcc-g++*, and *Apply Changes* in the *Installation* menu.
- Add *C:\MinGW\bin* to the *PATH* environment variable.
- Create a *distutils.cfg* file with the following contents in the folder *\Lib\distutils* in Python install directory :
[Toggle line numbers](https://wiki.python.org/moin/WindowsCompilers#)

```
[build]
compiler=mingw32

[build_ext]
compiler=mingw32
```