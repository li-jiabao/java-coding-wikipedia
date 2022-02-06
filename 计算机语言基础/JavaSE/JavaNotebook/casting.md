---
title: casting
author: lijiabao
date: 2021-02-16 23:54:11.646473000 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
类型转换：
从一个类型转换到另外一个类型的过程。
和基本数据类型的转换一致，有时候我们需要对对象类型进行转换。
`int i = 1;double a = (double)i;`
`Dog dog = new Dog();Animal animal = new Animal();`

当你忘记了一个对象的实际类型，但你又想要完全使用该对象的能力时，可以使用类型转换。

每个对象变量都有一个类型，这个类型是这个变量指向的对象类型或者说他能做什么。

当你为变量储存值的时候，编译器会检查你是否承诺了太多的东西给他。当你将父类变量指向子类对象时，并没有承诺太多，但是当你将子类变量指向父类对象时，就承诺了过多的东西。指向父类reference时，这个时候可能就需要做一个类型转换cast。

如果你的类型转换不合理，编译器会抛出`ClassCastException`，如果不对这个异常进行捕获，程序就会终止。但是可以利用`instaceof`进行判断来省略异常的捕获。
`if (obj instanceof Class){}`

实际上，不是大多时候都需要进行类型转换，因为由于多态的特性存在，很多时候不进行类型转换也可以正常工作。仅仅在你需要调用子类的某个特有的方法的时候，需要进行类型转换。