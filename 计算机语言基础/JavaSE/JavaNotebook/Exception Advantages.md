---
title: Exception Advantages
author: lijiabao
date: 2021-02-21 17:20:13.600369600 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
#### 异常的优点


**Advantage 1**：将异常处理代码和正常的代码分离开来
如果没有该异常处理机制，可能需要在每个位置进行判断detection从而确定是否可以往下继续进行，但是异常处理机制的出现，使得可以将一连串操作放在try代码块中，从而根据可能出现的异常利用不同的catch代码块去进行分别的处理，这样就让代码逻辑性更好，可读性更好，同时也使得程序更加的robust

**Advanage 2**：将异常错误推送到stack trace
传统的异常会让整个调用链的函数都要去推送异常知道最后异常到达处理的位置。而JVM运行环境会往后搜索调用堆栈去找寻处理特定异常。方法可以将异常推送到调用堆栈中，因此就可以让其他方法进入到调用堆栈去捕捉它，只有要处理异常的方法才关心探测异常。
但是推送异常到堆栈需要middleman method的参与帮助，因此任何可以在方法中被抛出的异常必须指定在throws语句中

**Advantage 3**：将错误类型分组并区分错误类型

异常就是对象，因此，异常的分组分类本质上就是区分异常类的等级制度。例子：java.io.IOException，这是在处理IO操作时发生的最一般的异常，而它的子类则代表了更加明确的异常FileNotFoundException。异常处理器可以选择写更加明确的异常，也可以写更宽泛的异常。异常的等级制度的最接近顶端就是Exception最高的是==Throwable==。
`exception.printStackTrace(); // output goes to System.err`
`exception.printStackTrace(System.out; // sent trace to stdout`
可以选择指定更加详细的异常处理器，这样，代码的robust和处理异常的能力更强。当然如果不用捕获明确的异常，可以使用更为宽泛的异常类Exception之类。
