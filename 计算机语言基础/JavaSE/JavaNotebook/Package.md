---
title: Package
author: lijiabao
date: 2021-02-16 23:41:47.915124500 +0800
category: JavaNotebook
categories: JavaNotebook
tags: JavaNotebook
---
> 是一个Java用来管理拥有相近功能的类和接口的组件。通过`javac -d destinationFile class.java`编译后，编译好的类文件便放在了目标文件夹中，之后可通过import语句导入该包下的类和接口

> package定义的时候，避免首项出现java，这在Java中被禁止使用，防止与Java内置的包名发生冲突。
==默认包中的变量、方法和类可以不用导入直接使用。==
### 包的一些基本内容
1. 包的导入：
`import packageName;`
package可以使用全名来引用包中的类和接口等内容，还有一种节省时间的方法，就是通过import语句将你需要多次使用的某个包中的类和接口导入，之后便可以直接通过类名来使用该类。
2. 包中静态成员的导入
`import static packageName;`
3. package的创建：
在源文件的第一行（首行）须有package的声明：`package myjava.myclass;`，==且每个源文件只能有一个package'声明==。一个源文件只能有一个公共类，且文件名必须是公共类的类名`ClassName.java`。创建好包之后，可以使用命令`javac -d destination_class_dir ClassName.java`进行编译，该命令将编译好的类文件放置到目标路径下。==包的路径在编译时会自动创建，因此可以不用提前创建好包路径==，对于该命令编译好的类文件，若需要运行，需把包路径带上`java destination_class_dir/ClassName`，==不然会出现无法加载主类或找不到主类的错误==。
==类搜索路径==：在classpath中设置的路径，就是在import时，搜索类文件的路径。文件编译的时候可以利用-cp参数后面带上类文件的路径即可，运行类文件的时候也可以利用-cp带上类文件所在的路径，以防出现找不到主类的错误。`java/javac -cp class_path;.;jar_path `这是在java和javac命令时的添加classpath的语句。
4. 包的scope：class被保存在文件路径结构中，class所在的路径需要和package的包名一致。class文件还可存放在.jar文件中（压缩文件，保存class文件，保存空间提升性能）。对于包的访问权限，定义为public的类和接口，在任何的包中都可访问，定义为默认的，只能在相同的包下才可访问，而对于private，只有自己类才可访问，protected是只有自己类和子类可以访问。对于变量来说，==除非有用public和默认的条件，不然最好建议使用private==。package sealing：是Java用来禁止往某特定包里面再添加类的一种方法，为了减少冲突

### class的路径
包主要是用来管理分发类文件的，防止类文件的命名冲突问题，包通过文件的结构来控制类文件。类文件还可以存放在jar文件中。在设置类的搜索路径时，可以如下：`-cp myclass_dir;.;jarclass_dir`,myclass_dir放置创建的类文件，.代表的是当前路径下的类文件，jarclass_dir时放置jar压缩文件的。还可以使用通配符`jarclass_dir\\*`代表将所有的jar文件放置到类路径下。
==javac编译的时候，如果设置路径的时候忘记将当前路径设置进去，如果没有设置，为默认的时候，不会出错，但是没设置.时便会出现运行不了的情况。==
编译器在查找类路径的时候比虚拟机查找需要更长时间。首先在`java.lang`中寻找，接着会在导入语句中去寻找：`import java.util.\*;``import myclass.study.\*`，最后再在当前路径下寻找。如果找到了多个相同的类名，会出现编译错误的情况
编译器还会多进行一个步骤，进入到源文件查看是否比类文件有更新，如果有，自动重新编译源文件。==从别的包文件中只能导入公共类==

### 设置classpath
==建议==使用-cp或者-classpath选项来设置类文件路径
`java -cp classpath;.;archive\\* className`
还可以使用环境变量来进行设置CLASSPATH。`set CLASSPATH=...|export CLASSPATH= :.:| setenv CLASSPATH :.:`三种不同环境下设置环境变量。
把所有的jar放在同一个文件夹下和使用CLASSPATH环境变量并不可取。前者由于可能会出错并且可能会遗忘，后者若是忘记设置当前路径，可能造成一些问题。
