---
title: unchecked exception
author: lijiabao
date: 2020-11-27 22:19:38.338291700 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
unchecked异常的几个争议点：

1. JAVA编程并不要求捕获和处理unchecked异常，因此程序员可能会想要写那些==只抛出unchecked异常==或者==让所有的异常类继承RuntimeException==。这是由于这些捷径的出现，使得程序员可以写出不用接收编译错误的代码，并且不用指定异常的处理。==这种行为虽然看起来很方便，但是它抛弃了捕获异常处理异常的原有目的，并且可能会造成其他使用你的类的人出现很多问题==
2. 设计者会强制一个方法去指定所有的未被捕获的检查异常可以在它的scope内抛出：任何可以被抛出的异常是这个方法的公共接口的一部分，那些使用这个方法的人==必须知道方法可以抛出的异常==，从而决定用这个方法去干什么。这些==异常和参数返回值一样都是方法的程序接口的一部分==。
3. 那为什么不去指定RuntimeException呢：由于RuntimeException代表的是那些编程问题产生的结果，往往==不能预料==并且去处理这些问题（比如算术问题，除零错误pointerNull错误以及数组越界问题等）这类错误可以在程序的任何地方出现，并且==错误种类太多==，如果都去指定的话，程序就变得过于==庞杂不清楚==。因此编译器并不要求去捕获这类异常并处理（比如：NullPointerException）

==一般情况，不抛出RuntimeException或者创建该异常子类，只是因为不想因为指定异常而被弄得筋疲力尽。==

==Bottom line guideline==：如果可以合理的预料并修复这个问题，使用checked异常，如果不能从异常修复，使它成为unchecked异常。





1. [[Error exception]]
2. [[Runtime exception]]