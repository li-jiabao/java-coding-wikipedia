---
title: Java注解
author: lijiabao
date: 2021-06-01 13:30
categories: ["Java学习笔记"]
tags: ["Java学习笔记"]
---

介绍Java注解的用处和用法，并介绍一下常用的几种元注解以及如何利用反射机制进行注解的处理

# Java注解

注解是一种可以使用在类、方法、字段定义时提供一些信息或者指示的一个功能组件。

注解的主要作用：

- 提供信息给编译器
- 编译和部署时的处理：可以处理注解信息生成代码或者xml文件等
- 运行时处理：有些注解可以在运行时被测试

除了注解类、方法和变量之外，注解的还有作为类型注解的常见使用：

- 类实例创建表达式中：`new @Interned MyObject();`
- 类型转换：`myString = (@NotNull String) str;`
- 接口实现：`implements @ReadOnly List<@ReadOnly T>`
- 抛出异常：`throws @Critical Exception`

## 注解定义

```java
@interface AnnotationName{
    // element定义语法：类型 名称() default ...;
    String varName1();
    int varName2();
    // 使用default定义默认值
    String varName3() default "hello";
    // 使用数组
    String[] varName4;
    
}
```

## 元注解

### 用于其他注解的元注解

1. @Retention：指定注解怎么被保存
   - `RetentionPolicy.SOURCE`：标识只存在源文件层级，且被编译器忽略
   - `RetentionPolicy.CLASS`：标识存在于class文件中，编译时被编译器保存，但是被虚拟机忽略
   - `RetentionPolicy.RUNTIME`：标识被虚拟机保留，运行时可用
2. @Target：标识哪种Java元素可以使用这个注解
   - `ElementType.ANNOTATION_TYPE`：可用在注解类型
   - `ElementType.CONSTRUCTOR`：用在构造器
   - `ElementType.FIELD`：用在字段上
   - `ElementTypeMETHOD`：用在方法上
   - `ElementTypeLOCAL_VARIABLE`：局部变量
   - `ElementTypePACKAGE`：用在包
   - `ElementTypePARAMETER`：用在参数
   - `ElementType.TYPE`：可用在任何Java元素上
3. @Documented：标识是否可以使用javadoc生成document
4. @Inherited：标识是否可被子类继承
5. @Repeatable：标识是否可以重复使用于一个元素多次

### 用于Java语法中的元注解

1. @Deprecated：标识即将被启弃用的功能
2. @Override：标识重写父类或者接口的方法
3. @SuppressWarnings：告诉编译器忽略指定的警告信息
4. @SafeVarargs：应用于方法或者构造器，断言不会在可变参数上不会进行不安全的操作，和可变参数非检查型异常会被忽略
5. @FunctionalInterface：定义可能是一个功能性接口（functional interface）

## 注解处理

对于Java中注解主力，主要利用反射机制来实现：

```java
@AnnotationName(name="", age=15)
class ClassName{
    // ...
}

public class Test {
    public static void main(String[] args) {
        // 使用反射API来获取信息
        // 反射读取有两种方法
        // 1. 先判断是否存在
        Class cls = ClassName.class;
        if (cls.isAnnotationPrsent(AnnotationName.class))
            AnnotationName anno = cls.getAnnotation(AnnotationName.class);
        // 2. 先直接获取，再判断是否为空
        AnnotationName anno = cls.getAnnotation(AnnotationName.class);
        if (anno !=null)
            //....
    }
}
```



## 注解用于ORM（对象关系型映射）的处理

```java
import java.lang.annotation.*;
import java.lang.reflect.Field;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface TableName {
    String value() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface FieldName {
    String columnName();
    String type();
    int length();
}

@TableName("Person")
class Person {
    @FieldName(columnName = "name", type = "String", length = 10)
    String name;
    @FieldName(columnName = "age", type = "int", length = 3)
    String age;
    @FieldName(columnName = "bornDate", type = "date", length = 10)
    String bornDate;
}

// 利用反射API来处理注解信息
public class Test {
    public static void main(String[] args) throws ClassNotFoundException, NoSuchFieldException {
        Class cls = Class.forName("Person");  // 获取被注解类的反射类
        Annotation[] annotations = cls.getAnnotations();  // 获取所有的类注解
        for (Annotation annotation : annotations) {
            System.out.println(annotation);
        }
        // 获取指定的类注解，并且使用强制类型转换
        TableName table = (TableName) cls.getAnnotation(TableName.class);
        // 获取字段的反射类
        Field fieldCls = cls.getDeclaredField("name");
        FieldName field = (FieldName) fieldCls.getAnnotation(FieldName.class);
        // 直接使用获取到的注解类来获取注解属性值
        System.out.println(table.value());
        System.out.println(field.columnName());
        System.out.println(field.type());
        System.out.println(field.length());
    }
}
```

