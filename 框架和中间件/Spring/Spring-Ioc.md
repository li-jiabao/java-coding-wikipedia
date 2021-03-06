# IOC控制反转

## 什么是控制反转

控制反转：就是将对象创建调用等控制权从原来的程序代码中交给外部的容器

也就是将对象的new实例化过程交给spring容器来执行，而在未使用spring的时候，对象的实例化都是在类内部进行的，类与类之间高度耦合，难于测试。使用了spring之后，类的是实例化都交予spring的IOC容器，由容器来注入组合对象，降低了类与类之间的高耦合

```java
public class Car{
    private Engine engin;
    private Tyre tyre;
    
    // 控制反转的实现模式，将engine和tyre的注入交给外部的容器，而不是在这个代码逻辑中实现
    public Car(Engine engine, Tyre tyre) {
        this.engine = engine;
        this.tyre = tyre;
    }
    // 普通实现,一旦需要修改引擎，带来的代码修改比较多且复杂
    public Car(Engine engine, Tyre tyre) {
        this.engine = new Engine();
        this.tyre = new Tyre();
    }
}
```

## 什么是依赖注入

组件之间的依赖关系交给IOC容器在运行期间决定，也就是由容器动态的将某个依赖关系注入到组件之间

## 为什么使用控制反转和依赖注入

为了更好的了解，首先了解软件设计的一个重要思想——依赖倒置原则

### 依赖倒置原则

以制造一辆汽车为例，在我们制造一辆汽车时，汽车依赖于汽车车身，汽车车身依赖于底盘，而底盘又依赖于轮胎，存在这样的一连串的依赖关系。
对于这种高度耦合的依赖关系，缺点就是不好维护。当我们将整个设计都完成了之后，这时候如果轮胎的设计需要更改，那我们所有的设计都需要发生更改，因此维护工作量就很大。

但是如果我们换一种设计思路：首先设计轮胎，然后再设计底盘，再根据底盘设计车身，最后再设计汽车，这个时候，如果其中一环需要更改，只需要改动这一环，后续依赖的不需要再发生更改

### 控制反转带来的好处

还是以汽车制造为例，假如汽车制造需要引擎和轮胎，传统的代码开发就是将轮胎类和引擎类直接写到汽车类中来，这个时候如果有许多的汽车类都使用了这个引擎类，而恰好这些汽车都需要更换引擎，这个时候代码修改的代价就很高了，需要找到所有的引擎类，然后将其删除修改。

如果使用控制反转，利用一个容器来完成这个引擎类的注入工作，设计了多个汽车类，但是这些汽车类都是依赖于容器中的引擎类来注入依赖，当你需要修改引擎的时候，你可以直接在容器中将这个引擎类修改即可，之后注入的都是这个引擎类，汽车类都不需要发生很大的修改，代码维护的成本极大的降低

## spring IOC容器



容器的内部实现逻辑，就是利用了Java的反射机制

### BeanFactory容器

通常并不建议使用这个容器，但是这个容器可以用在轻量级的应用程序中，比如移动设备或者applet程序

```java
// 利用BeanFactory接口实现类来创建spring IOC容器
BeanFactroy factory = new XmlBeanFactory(new ClassPathResource("xml文件路径"));
// 使用容器的getBean()方法来获取xml中的bean对象，并使用强制类型转换转换对象
Obj obj = (Obj) factory.getBean("bean-id");
```

### ApplicationContext容器

ApplicationContext容器包括BeanFactory容器的所有功能

常被使用的ApplicationContext接口实现：

- FileSystemXmlApplicationContext：该容器从xml文件中加载已被定义的bean，需要提供给构造器xml文件的完整路径
- ClassPathXmlApplicationContext：该容器从xml文件中加载已被定义的bean，不需要提供xml的完整路径，只要正确配置了classpath环境变量即可，容器会从classpath路径中搜索bean配置文件
- WebXmlApplicationContext：该容器在一个 web应用程序的范围内加载在xml文件中已被定义的bean

```java
// 利用ApplicationContext接口的实现类来创建spring IOC容器
ApplicationContext context = new ClassPathXmlApplicationContext("xml文件路径");
// 使用容器的getBean()来获取配置文件中设置的bean对象，还需要使用强制类型转换
Obj obj = (Obj) context.getBean("bean-id");
```

## Spring bean

### bean定义

bean对象是构成应用程序的支柱，是一个被实例化，组装并由Spring IOC容器来管理的对象

bean的创建是通过使用容器提供的配置元数据创建，如xml配置文件中定义，或者使用Java注解来实现

### bean的命名

每个bean对象都有一个或者多个标识符，这些标识符在容器内必须是唯一的，普遍情况下bean只有一个标识符，如果需要额外的，可以使用别名。

bean的命名传统：小驼峰命名

命名在xml配置文件中可以使用bean标签的id属性或者name属性，name属性使用逗号分号或者空格隔开就是设置了别名，不一定非要给bean设置标识，spring会自动设置，但是如果想要使用ref引用该bean需要设置标识

可以在beans标签下面使用alias标签设置别名`<alias name="id_name" alias="id_name_alias" />`

### bean的实例化

#### 使用构造器实例化

 

```xml
<bean id="example" class="java.lang.String" />
<bean name="anotherExample" class="java.lang.String" />
```

#### 使用静态工厂函数实例化

```xml
<bean id="example" class="com.jiabao.ClassExample" factory-method="createInstance"/>
<!-- 静态工厂函数就是类内部定义的静态方法，返回的是类对象 -->
```

```java
public class ClassExample {
    private static ClassExample example = new ClassExample();
    
    public static ClassExample createInstance(){
        return example;
    }
}
```

#### 使用实例工厂函数

```xml
<bean id="classExampleLocator" class="com.jiabao.ClassExampleLocator" />

<!-- 
为了使用非静态的创建实例的方法，需要使用factory-bean指定bean定位到方法所在的类
再利用factory-method指定方法名
-->
<bean id="service1" factory-bean="classExampleLocator" factory-method="createService1Instance" />

<bean id="service2" factory-bean="ClassExampleLocator" factory-method="createService2Instance"
```

```java
public class ClassExampleLocator {
    private static ClassExampleService1 service1 = new ClassExampleService1();
    private static ClassExampleService2 service2 = new ClassExampleService2();
    
    public ClassExampleService1 createService1Instance(){
        return service1;
    }
    
    public ClassExampleService2 createService2Instance(){
        return service2;
    }
}
```

使用上下文的getType()方法可以查看确定bean的运行时的类型

### Spring配置元数据

- xml的配置文件
- 给予注解的配置
- 给予Java的配置

### xml配置bean

bean标签的属性和属性值：

| 标签属性       | 属性值描述                                                   |
| -------------- | ------------------------------------------------------------ |
| id或name       | 指定的是获取bean对象时需要传入的值，也就是bean的唯一标识符，name可以使用逗号分号或者空格来指定别名，也可以使用alias指定别名，命名传统是首字母小写其他每个单词首字母大写的小驼峰 |
| class          | 指定的是类对象所在的位置`"com.jiabao.demo.Hello"`            |
| scope          | 指定bean对象的作用域，singleton、prototype以及三种web应用级的作用域 |
| init-method    | 指定bean对象初始化后执行的方法                               |
| destroy-method | 指定bean对象从容器中销毁时执行的操作                         |
| lazy-init      | 延迟初始化，标签值为布尔值                                   |
| parent         | 指定bean对象的父类继承关系                                   |

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

    <!-- bean导入其他xml配置文件的bean内容 -->
    <import resource="其他配置文件的绝对路径或者相对路径" />
    
   <!-- 简单的bean对象定义 -->
   <bean id="..." class="...">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

   <!--定义了lazy init的bean对象-->
   <bean id="..." class="..." lazy-init="true">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

   <!-- 定义了对象初始化之后执行的方法 -->
   <bean id="..." class="..." init-method="...">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

   <!-- 定义了对象销毁时执行的方法 -->
   <bean id="..." class="..." destroy-method="...">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

   <!-- more bean definitions go here -->

</beans>
```

### bean作用域

- singleton：在每个spring IOC容器内部只存在一个bean实例，以单例方式存在，这是spring的默认作用域配置项
- prototype：每一次获取bean对象都会获得一个新的bean对象对应的实例
- request：每一次HTTP请求都会创建一个新的bean，仅适用WebApplicationContext环境
- session：对于同一HTTP session，都使用同一个实例，仅适用与WebApplicationContext环境
- application：作用域限定于servletcontext的生命周期，仅适用与WebApplicationContext环境
- websocket：作用域限定于websocket的生命周期内，仅适用webapplicationcontext环境

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- 利用scope属性设置作用域，singleton是默认作用域 -->
   <bean id="..." class="..." scope="singleton">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

</beans>
```

### singleton和prototype之间的依赖

当使用singleton的bean对象注入prototype的bean依赖的时候，要注意，依赖解析识别时在初始化的时候就开始了，因此注入prototype的bean到singleton的bean中时，先实例化一个新的prototype实例对象，然后将其注入到singleton的bean对象，这个实例化的prototype时唯一的一致提供给singleton的实例化实例

在运行时，如果你想要在singleton的bean中获取一个prototype的新的实例对象，不能再生成一个新的对象注入，因为单例的注入只发生一次，这个时候，需要使用**方法注入（method injection）**

### bean生命周期

1. bean定义
2. bean初始化
3. bean的使用
4. bean的销毁

#### bean初始化回调

1. xml文件指定初始化回调

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- 指定初始化回调方法，这个方法可以是类中自定义的方法 -->
   <bean id="..." class="..." init-method="init-method-name">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

</beans>
```

2. 实现InitializingBean接口的afterPropertiesSet()方法，在这个方法中定义初始化后执行的方法体

```java
class Hello implements InitializingBean {
    public void afterPropetiesSet(){
        // 定义初始化后的执行方法内容
    }
}
```

#### bean销毁时回调

如果不在web应用中，对象销毁会在你注册hook时执行，将对象正常销毁关闭

1. xml配置文件指定

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- 指定bean对象销毁时执行的方法名 -->
   <bean id="..." class="..." destroy-method="method-name">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

</beans>
```

2. 实现接口DisposableBean接口的destroy方法

```java
class Hello implements DisposableBean {
    public void destroy() {
        // 对象销毁时执行的方法体内容
    }
}
```

#### 默认初始化和销毁方法

如果你有太多的具有相同名称的初始化或者销毁回调方法，可以不用在每一个bean上声明初始化方法和销毁方法，只需要在框架中使用default-init-method和default-destroy-method属性灵活配置多个bean初始化和销毁回调同名的情况

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd"
    default-init-method="init" 
    default-destroy-method="destroy">

   <bean id="..." class="...">
       <!-- collaborators and configuration for this bean go here -->
   </bean>

</beans>
```

<font color='red'>注意：</font>如果在非web应用程序环境中使用spring的IOC容器，那么在jvm 中需要注册关闭hook，这样可以确保正常关闭，为了让所有资源都释放，可以在单个bean上调用destroy方法。<font color='red'>对于接口实现和xml配置，更推荐xml配置，更加灵活</font>

```java
context.registerShutdownHook();  // 注册hook确保正常关闭
```

#### bean后置处理器

后置处理器允许在调用初始化方法前后对bean对象进行额外的处理，BeanPostProcessor接口定义了回调方法，因此要实现后置处理器，就需要定义实现该接口的类，这个类需要实现postProcessBeforeInitialization()和postProcessAfterInitialization()这两个抽象方法

<font color='red'>注意：</font>ApplicationContext会自动检测BeanPostProcessor接口的实现类对应bean，注册这些bean为后置处理器，然后在容器创建bean对象的时候，在合适的时候调用后置处理器绑定的回调方法

```java
import org.springframework.beans.factory.config.BeanPostProcessor;

// 后置处理器接口可以配置多个，使用Ordered接口提供的属性order设置接口的执行顺序
public class InitProcessorHello {
    public Obejct postProcessAfterInitialization() throws BeansException{
        System.out.println("初始化之后调用的方法");
        return bean;  // 可以返回任意对象
    }
    public Obejct postProcessBeforeInitialization() throws BeansException{
        System.out.println("初始化之前调用的方法");
        return bean;  // 可以返回任意对象
    }
}
// 这个后置处理器定义好之后也是需要添加到bean对象中去的，只不过ApplicationContext会自动检测这个bean
// 并将其注册为后置处理器，并且会在合适的时候调用
// <bean class="com.jiabao.demo.InitProcessorHello" />
```

### bean定义继承

和oop中的继承关系不一样，但是潜在的逻辑是一样，都是存在继承关系，只不过是bean对象上的，比如会继承父类的property标签内容

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="helloWorld" class="com.tutorialspoint.HelloWorld">
      <!-- property标签内的name对应的值需要在bean所属的对象中定义并且设置号set和get方法 -->
      <property name="message1" value="Hello World!"/>
      <property name="message2" value="Hello Second World!"/>
   </bean>

   <bean id="helloIndia" class="com.tutorialspoint.HelloIndia" parent="helloWorld">
      <property name="message1" value="Hello India!"/>
      <!-- 子类没有设置message2，但是如果获取该属性的时候，是可以获取到父bean中message2的 -->
      <property name="message3" value="Namaste India!"/>
   </bean>

</beans>
```

#### bean定义模板用以继承

bean模板很容易可以被其他的子bean使用，只需要使用parent属性指定到模板标识即可。作为模板的父bean是不可以实例化的，因为它是不完整的而且明确定义为abstract，仅仅只是作为一个模板bean使用

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
<!-- 定义模板时，不需要指定class属性，只需要设置abstract属性为true即可 -->
   <bean id="helloWorld" abstract="true">
      <property name="message1" value="Hello World!"/>
      <property name="message2" value="Hello Second World!"/>
   </bean>

   <bean id="helloIndia" class="com.tutorialspoint.HelloIndia" parent="helloWorld">
      <property name="message1" value="Hello India!"/>
      <property name="message3" value="Namaste India!"/>
   </bean>

</beans>
```