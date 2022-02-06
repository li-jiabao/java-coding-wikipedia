# C语言

## 变量

静态存储：程序运行期间由系统分配固定的存储空间的方式

动态存储：程序运行期间根据需要进行动态的分配存储空间的方式

全局变量：定义在函数体外的变量，一般是全局可访问的变量

局部变量：定义在函数体内部的变量

局部变量的存储类别：

- 自动变量：函数中的局部变量，没有声明static，都是动态分配存储空间，函数调用结束就自动释放内存空间
- 静态局部变量：static声明，函数调用结束不消失继续保留，下一次函数调用继续使用这一个变量值
- 寄存器变量：如果有一些变量使用频繁，可以设置为寄存器变量加速变量读取写入

全局变量的存储类别：

- 文件内扩展变量作用域：extern定义的变量，可以扩展到函数内部使用外面extern定义的外部全局变量
- 扩展到文件外：extern可以将其他文件中定义的变量扩展到本文件
- 外部变量限制在本文件：static定义全局变量将其限制在本文件中

## 函数

内部函数：static修饰的函数，只能本文件中的函数调用

外部函数：其他外部文件也可调用；默认声明就是外部，也可使用extern声明

## 动态内存分配

库：`malloc.h`

库函数：`malloc`分配一个连续空间、`calloc`分配多个连续空间、`free`释放内存、`realloc`重新分配

## 指针

```c
// 引用数组
int arr[4];
int *p = arr;
// 引用字符串
char *p = "this a string";
// 引用函数
int *p(int,char);
// 返回值
ziplist * funName();
// 指针数组
int *p[4];
// 指向指针的指针变量
int **p;
```

## void指针

指向空类型、或者没有指向确定的类型

## 自定义结构体

```c
struct structName {
    int structField;
    char field2;
    char * string;
    int p[4];
};
```

结构体访问：

```c
typedef ziplist{
    // ....;
    char * String = "dff";
} ziplist;
/// 
ziplist.name;   // 结构体变量访问
p->name;       // 指针变量名访问
(*p).name;     // 指针
```



## 类型声明

声明新的类型名

```c
char *p;
char *String;
typedef char* String;    // 将字符指针类型定义为String
String p;              // 这时候就相当于定义了一个字符指针
```



```c
typedef ziplist{
    // ....;
} ziplist;
// 将结构体定义为一种类型，之后声明类型可以直接使用ziplist代表这样的一种结构体类型
```

