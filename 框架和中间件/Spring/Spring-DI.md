# DI依赖注入

## 什么是依赖注入？

依赖注入就是将某个对象的依赖对象注入到这个对象中，依赖注入可以将不同的对象之间耦合联系起来

Spring框架中的依赖注入是通过IOC容器来完成的，由容器来决定注入哪些依赖

## 为什么要使用依赖注入？

当应用程序比较复杂的时候，不同的类对象之间相互耦合度过高，对象和对象之间并不相互独立，如果其中的一个对象发生修改，其他依赖的对象可能也会需要发生很大的修改，这会使程序的维护成本过高并且测试复杂

但是使用容器来执行依赖注入的话，类和类之间是相互独立的，类和类之间的依赖关系由容器来进行管理，因此如果对象发生了修改，这个时候只需要修改容器中的对象，其他的依赖对象并不需要进行修改或者只需要进行少量的修改，这就为代码维护和测试效率提高，维护和测试成本降低

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

## Spring的xml配置注入

介绍Spring中依赖注入的几种形式：基于构造函数的依赖注入、基于设置函数setter的依赖注入、注入内部beans、注入集合，下面的beans配置文件主要依照下面的三段代码来设置

```java
// 需要执行注入的类
package com.jiabao.demo;

// 定义一个类来注入依赖
public class HelloWorld {
    private Hello hello;
    private String name;
    
    // 构造器函数
    public HelloWorld(Hello hello) {
        this.hello = hello;
    }
    // 设值函数
    public void setHello(Hello hello, String name) {
        this.hello = hello;
        this.name = name;
    }
}
```

```java
// 要注入到其他类的依赖类
package com.jiabao.demo;

// 定义一个HelloWorld的依赖类
public class Hello {
    public void printHello(){
        System.out.println("输出hello信息");
    }
}
```

```java
// 测试类定义
public class SimpleTest{
    public stativ void main(String[] args){
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
        HelloWorld hello = context.getBeans("helloWorld");
    }
}
```

### 基于构造函数的依赖注入

基于构造函数的依赖注入使用constructor-arg标签来进行配置

1. 使用标签内的ref属性指定bean的引用，使用value传递值
2. 使用标签内部的type可以指定参数的类型
3. 使用标签内部的index属性可以指定参数
4. 对于多个参数的构造函数，需要按照顺序指定其参数或者使用index指定指定位置的参数
5. 可以使用c:namespace

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    
    <!-- 介绍构造器依赖注入的几种常用的配置 -->
    <bean id="helloWorld" class="com.jiabao.demo.HelloWorld">
        <!-- 传递bean对象引用使用ref属性，传递值使用value属性 -->
        <constructor-arg ref="hello" />
        
        <!-- 多个构造器参数指定多个构造器的标签即可 -->
        <!-- 只是多个构造器参数需要按照顺序来指定 -->
        <constructor-arg ref="另外一个构造器参数"/>
        
        <!-- 多个构造器参数还可以使用index来指定位置 -->
        <constructor-arg index="0" ref="hello" />
        <constructor-arg index="1" ref="hello" />
        
        <!-- 构造器参数还可以指定参数的类型 -->
        <constructor-arg type="int" value="2001" />
        <constructor-arg type="java.lang.String" value="hello world" />
        
        <!-- 使用c:namespace来完成构造函数的参数简略传递-->
        <bean id='hello1' class="com.jiabao.demo.Hello" c:name="..." c:name-ref="beanName"/>
        <!-- 还支持索引来利用c:namespace进行依赖传递 -->
        <bean id='hello1' class="com.jiabao.demo.Hello" c:_0="..." c:_1-ref="beanName"/
    </bean>
    <bean id="hello" class="com.jiabao.demo.Hello"/>
</beans>
```

### 基于设值函数的依赖注入

基于设置函数的依赖注入通过设置property标签来进行设置：

1. 使用value属性指定参数的值
2. 使用ref属性指定引用的bean对象
3. 对于多个参数，可以指定多个property标签，根据name来识别参数
4. 可以使用p:namespace重写简化代码
5. 可以配置java.util.Properties
6. 使用idref标签来确保命名bean确实存在

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="john-classic" class="com.example.Person">
      <!-- 指定多个property标签来指定多个参数的注入 -->
      <property name="name" value="John Doe"/>
      <property name="hello" ref="hello"/>
   </bean>

   <bean name="hello" class="com.jiabao.demo.Hello" />
    
    <!-- 还可以使用p-namespace实现配置 -->
    <bean id="new-jane" class="com.jiabao.demo.HelloWorld" p:name="New Jane" p:hello-ref="hello" />
    
    <!-- 使用Properties类型的依赖配置 -->
    <property name="properties">
        <value>
            jdbc.driver.ClassName=com.mysql.jdbc.Driver
            jdbc.util=jdbc.mysql://localhost:3306/test
        </value>
    </property>
    
    <!-- 使用idref标签 -->
    <bean id="..." class="...">
        <property>
        	<idref bean="targetBean" />
        </property>
    </bean>

</beans>
```

**对于构造函数注入和设值函数的依赖注入，强制性依赖建议使用构造函数注入，可选依赖使用设值函数依赖注入**

### 注入内部bean

内部bean的基本理念和Java中的内部类很类似，但是bean的内部bean实现方式不一样，内部bean就是在其他bean内部定义的bean，通过在property或者constructor-arg标签内部定义bean对象即可实现内部bean

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    
    <bean id="helloWorld" class="com.jiabao.demo.HelloWorld">
    	<property name="hello">
            <bean id="hello" class="com.jiabao.demo.Hello"/>
        </property>
    </bean>

</beans>
```

### 注入集合

Spring中定义了四种集合的使用：**只能够出现在设置函数参数中，需要定义setter方法**

- set：集，不允许出现重复值
- list：列表，允许出现重复值
- map：名称-值对，允许名称和值为任意类型的值
- props：名称-值对，只允许字符串类型的情况

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    
    <!--  设置使用集合 -->
    <bean id="collection" class="...">
        <property name="...">
            <props> <!-- 只允许出现字符串类型的键值对 -->
                <prop key="key1">value1</prop>
                <prop key="key2">value2</prop>
            </props>
            <list> <!-- list允许出现重复值 -->
                <value>value1</value>
                <value>value2</value>
                <ref bean="..."/>
            </list>
            <set> <!-- set不允许出现重复值-->
                <value>value1</value>
                <ref bean="..."/>
            </set>
            <map> <!-- 支持多类型，可以引用其他bean -->
                <entry key="key" value="value1"/>
                <entry key="ref" value-ref="refBean"/>
            </map>
        </property>
    </bean>
    <!-- 集合合并 -->
    <bean id='parent' class="...">
    	<property name="">
        	<props>
            	<prop key="key1">value</prop>
            </props>
        </property>
    </bean>
    
    <bean id="child" parent="parent">
    	<property><!-- 设置merge为true就是集合可以合并,但是不能合并不同的集合类型-->
        	<props merge="true">
            	<prop key="key1">value</prop>
                <prop key="key2">value2</prop>
            </props>
        </property>
    </bean>
</beans>
```

### 使用depends-on

depends-on属性是用来指示，在这个bean初始化之前，需要这个depends-on指定的bean先被实例化，这个bean才可以去实例化

```xml
<bean id="one" class="..." depends-on="two"/>
<bean id="two" class="..."/>
<bean id="three" class="..." depends-on="one,two"/>
<!-- 可以指定多个depends-on，逗号分号和空格都是合法的分隔符 -->
```

### lazy-initialized

通常，默认情况下，bean设置为单例singleton的，会立即为所有的singleton创建一个实例，这样可以尽早的发现错误。当你不需要这功能的时候，可以将设置为singleton的bean标记为lazy-initialized。标记为这个的bean会告诉IOC容器，当第一次调用这个bean的时候采取创建它的实例，而不是在程序已启动就创建实例

```xml
<bean id=".." class="..." lazy-init="true"/>
```

### 自动装配autowire

自动装配的优点：

- 自动装配可以明显的减少指定参数或者构造参数的需求，另外还可以使用bean模板减少属性配置
- 自动装配可以随着对象的发展自动更新配置，例如你需要添加一个依赖给类，这个依赖可以自动满足而不需要修改配置项

使用xml配置文件的时候，可以为bean定义指定autowire模式，有四种模式：

| 自动装配模式 | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| no           | 默认不开启自动装配，bean引用必须使用ref元素，在大的部署中并不建议修改这个默认配置，因为指定装配更加清晰易懂，可以更好的控制项目 |
| byName       | 通过property的name自动装配                                   |
| byType       | 更具参数类型来匹配进行自动装配，如果匹配多个会报错           |
| constructor  | 类似byType，只是应用于构造参数                               |

自动装配的局限和不足：

- 不能自动装配简单的属性比如基本数据类型，在属性和构造参数中的配置总是覆盖自动装配
- 自动装配并没有主动装配清晰易懂
- 对于从spring容器生成文本信息的工具来说，自动装配信息并不可获取
- 可能会存在多个bean匹配参数要求的类型

一些自动装配相关的选择：

- 想要更明确的装配就需要抛弃自动装配
- 避免某一个bean加入自动装配，设置autowire-candidate属性为false，还可以在最高配置项下beans标签中使用default-autowire-candidates属性来设置限制可以自动装配的bean的匹配模式，比如*Reposotory就是名字以Repository结尾的bean可以进行自动装配，使用多个匹配的化可以使用逗号分隔的列表来实现，但是autowire-candidate的优先级更高
- 设计单个bean为主要的候选，通过设置primary属性为true
- 使用基于注解的配置可以实现更精细化的控制

### 方法注入

spring引入方法注入的原因：为了可以在单例bean对象中注入原型bean的时候可以获取新的原型实例，而不是一开始实例化的时候创建的实例对象

#### 单例对象实现ApplicationContextAware接口达成目的

```java
public class TestSingletonClass implements ApplicationContextAware {
    private OuterPrototypeClass prototypeClass;
    
    public OuterProtoTypeClass getPrototype(){
        return (OuterPrototypeClass) factory.getBean("prototypeClass");
    }
}
// 这种实现接口的实现并不可取，因为商业代码更注重和spring框架整合，方法注入是IOC容器的高级功能
// 可以很不错的实现这种需求，并且简洁清晰

// 使用有状态的command风格的类执行某些处理操作
package fiona.apple;

import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;

public class CommandManager implements ApplicationContextAware {

    private ApplicationContext applicationContext;

    public Object process(Map commandState) {
        // 获取合适命令的实例化对象
        Command command = createCommand();
        // 在这个command对象上设置状态
        command.setState(commandState);
        return command.execute();
    }

    protected Command createCommand() {
        // notice the Spring API dependency!
        return this.applicationContext.getBean("command", Command.class);
    }

    public void setApplicationContext(
            ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```

#### lookup方法注入

```java
// lookup方法注入：容器覆盖容器管理bean对象上的方法并返回容器中的其他命名bean的找寻结果
// spring框架实现这种方法注入是通过使用来自CGLIB库的字节码生成来动态的生成覆盖这个方法的子类
public abstract class CommandManager{
    public Object process(Object commandState){
        Command command = createCommand();
        command.setState(commandState);
        return command.execute();
    }
    
    // 注解方式的设置
    @Lookup("myCommand")
    protected abstract Command createCommand();
    // <public | protected> abstract <返回类型> methodName(不需要参数);
}
```

```xml
<bean id="myCommand" class="..." scope="prototype">
	<!-- -->
</bean>

<bean id="commandManager" class="...">
	<lookup-method name="methodName" bean="myCommand"/>
</bean>
```

注意点：

- 为了动态子类可以正常工作，springbean容器子类不能是final，同样被覆盖的方法也不能是final

#### 方法替换实现

相比lookup方法注入，方法替换并不那么实用，可以暂时跳过这部分内容，后面需要再来使用

## Annotation注入



```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>
    
</beans>

<!--
为了使用注解的配置，需要将上述的xml配置文件加入
<context:annotation-config/>隐式的注册下面的后处理器
ConfigurationClassPostProcessor
AutoWireAnnotationBeanPostProcessor
CommonAnnotationBeanPostProcessor
PersistenceAnnotationBeanPostProcessor
EventListenerMethodProcessor
-->
```

### @Autowired

- 注解构造方法
- 注解常规的setter方法
- 注解有参数的方法
- 注解字段，甚至还可以注解字段和构造方法一起

### @Primary

调整设置自动装配的内容，@Primary是用来设置优先级的，当有多个bean候选的时候，有限选择@Primary注解的

```java
@Configuration
public class MovieConfiguration {
    @Bean
    @Primary
    public MovieCatalog firstCatalog() {...}
    
    @Bean
    public MovieCatalog secondCatalog() {...}
}

public class MovieRecommender {
    @Autowired  //  运用在字段上的自动装配
    private MovieCatelog movieCatalog;
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog" primary="true">
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```

### @Qualifier

#### 简单的qualifier使用

```java
public class MovieRecommender {
    @Autowired
    @Qualifier("main")
    private MovieCatalog movieCatalog;
}

public class MovieCatalog {
    private MOvieCatalog movieCatalog;
    
    @Autowired
    public void prepare(@Qualifier("main") MovieCatalog movieCatalog)() {
        this.movieCatalog = movieCatalog;
    }
}
```

相对应的xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog">
        <qualifier value="main"/> 

        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <qualifier value="action"/> 

        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```

#### 自定义qualifier注解配置

```java
@Target({ElementType.FIELD, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Genre {

    String value();
}

public class MovieRecommender {

    @Autowired
    @Genre("Action")
    private MovieCatalog actionCatalog;

    private MovieCatalog comedyCatalog;

    @Autowired
    public void setComedyCatalog(@Genre("Comedy") MovieCatalog comedyCatalog) {
        this.comedyCatalog = comedyCatalog;
    }
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

    <bean class="example.SimpleMovieCatalog">
        <qualifier type="Genre" value="Action"/>
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean class="example.SimpleMovieCatalog">
        <qualifier type="example.Genre" value="Comedy"/>
        <!-- inject any dependencies required by this bean -->
    </bean>

    <bean id="movieRecommender" class="example.MovieRecommender"/>

</beans>
```

#### 使用泛型



#### 使用CustomAutowireConfigurer

```xml
<bean id="customAutowireConfigurer"
        class="org.springframework.beans.factory.annotation.CustomAutowireConfigurer">
    <property name="customQualifierTypes">
        <set>
            <value>example.CustomQualifier</value>
        </set>
    </property>
</bean>
```

AutowireCandidateResolver确定autowire候选：

- 每个bean定义的autowire-candidate属性
- beans里面的default-autowire-candidate模式
- @Qualifier注解和任何通过AutowireConfigure注册的自定义的注解

### @Resource

用于字段或者bean属性设置方法上，Java EE的常见模式

@Resource有一个name属性，默认解释这个属性值为需要注入的bean的名字

```java
public class SimpleMovieListener {
    private MovieFinder movieFinder;
    
    @Resource(name="myMovieFinder")
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
// 如果不指定name属性，那么默认就是字段或者设置方法的名字
// 对于字段，就取字段名，对于设值方法，取bean的属性名
```

### @Value

通常用于注入外部属性

```java
@Component
public class MovieRecommender {
    private final String catalog;
    
    public MovieRecommender(@Value("$(catalog,name)") String catalog) {
        this.catalog = catalog;
    } 
}

// 配置文件内容
@COnfiguration
@PropertySource("classpath:application.property")
public class AppConfig {}

// application.property文件内容
catalog。name=MovieCatalog
```

## 基于Java进行的依赖注入

使用注解来进行完全依靠Java代码实现，而不需要采用xml配置文件的依赖注入。

### @Configuration和@Bean

使用这两个基本注解来实现配置spring容器：

- `@Bean`注解用来注解一个方法，这个方法用来实例化、配置并且初始化一个新的对象传递给spring容器来管理，这个就类似于xml配置文件中的`<bean>`，这个可以用于和任何注解为`@Component`的类搭配使用。但是通常和`@Configuration`注解搭配使用。
- `@Configuration`注解用来标识这个类是一个bean定义的来源，此外，标识为这个注解的类还可以让inter-bean之间的依赖通过调用其他的注解为`@Bean`的方法

```java
@Configuration
public class JavaConfig {
    @Bean
    public ClassName className(){
        return new ClassName();
    }
}
// 简单的配置
// 使用@Configuration和@Bean组合来完成
// 如果没有使用@Configuration，直接使用@Bean，这个时候配置类会以lite模式被处理
// 和使用@Configuration的完全配置不一样的是，lite模式方法不能定义inter-bean（bean之间）的依赖
// 只能以它包含component的内部状态进行操作
```

### 实例化容器

```java
// 实例化一个IOC容器
ApplicationContext context = new AnnotationConfigApplicationCnntext(JavaConfig.class);
ClassName bean = context.getBean(ClassName.class);  // 获取bean对象

// AnnotationConfigApplicationContext不仅仅只是局限在@Configuration注解的类
// @Component注解的类也是可以作为参数来构造IOC容器的
```

使用register方法有体系的构建IOC容器：

```java
// 定义一个无参数的IOC容器
ApplicationContext context = new AnnotationConfigApplicationContext();
// 使用register方法传入配置类或者组件类的参数来注册新的IOC容器
context.register(JavaConfig.class, ComponentClass.class);
context.register(AnotherComponent.class);
// 当所需要的组件类或者配置构建好之后，使用refresh方法将这些更改更新到容器中
context.refresh();
ClassName bean = context.getBean(ClassName.class);  // 获取需要的类对象

// 对于组件扫描的开启，除了可以在@Configuration注解下加入@ComponentScan传入参数
// 还可以使用ApplicationContext的scan方法开启
context.scan("com.jiabao");  // 开启组件扫描
context.refresh();  // 每次对context的修改都需要使用这个方法刷新配置
```

另外一个用于web应用的注解配置对象：`AnnotationConfigWebApplicationContext`，是`WebApplicationContext`的变体

```xml
<web-app>
    <!-- Configure ContextLoaderListener to use AnnotationConfigWebApplicationContext
        instead of the default XmlWebApplicationContext -->
    <context-param>
        <param-name>contextClass</param-name>
        <param-value>
            org.springframework.web.context.support.AnnotationConfigWebApplicationContext
        </param-value>
    </context-param>

    <!-- Configuration locations must consist of one or more comma- or space-delimited
        fully-qualified @Configuration classes. Fully-qualified packages may also be
        specified for component-scanning -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>com.acme.AppConfig</param-value>
    </context-param>

    <!-- Bootstrap the root application context as usual using ContextLoaderListener -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!-- Declare a Spring MVC DispatcherServlet as usual -->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- Configure DispatcherServlet to use AnnotationConfigWebApplicationContext
            instead of the default XmlWebApplicationContext -->
        <init-param>
            <param-name>contextClass</param-name>
            <param-value>
                org.springframework.web.context.support.AnnotationConfigWebApplicationContext
            </param-value>
        </init-param>
        <!-- Again, config locations must consist of one or more comma- or space-delimited
            and fully-qualified @Configuration classes -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>com.acme.web.MvcConfig</param-value>
        </init-param>
    </servlet>

    <!-- map all requests for /app/* to the dispatcher servlet -->
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/app/*</url-pattern>
    </servlet-mapping>
</web-app>
```

### @Bean使用

#### 声明@Bean

```java
@Configuration
public class JavaConfig {
    
    @Bean
    public ReturnClassType funcName(){
        // 也可以指定返回类型为接口但是返回值要是类对象，二者之间要有继承关系
        return new ReturnClassType();
    }
}
```

尽管可以使用接口的返回类型但是指定其他的类，但是这样的操作会影响到这个指定接口类型的高级类型预测的可见性

#### Bean依赖

@Bean注解的方法可以拥有任意数量参数，这些参数描述了构建bean所需的依赖

```java
@Configuration
public class JavaConfig {
    
    @Bean
    public ClassName methodName(AnotherClass dependency1, String dependency2){
        return new ClassName(dependency1, dependency2);  // 这个类似于构造函数依赖注入
    }
}
```

#### bean生命周期

可以使用JSR-250注解中的@PostConstruct和@PreDestroy注解来实现对象实例化之后或对象摧毁之前执行的操作

```java
public class HelloLifeCycle {
    
    @PostConstruct // 这个注解标识这个方法作为该对象实例化之后进行的操作
    public void init() {
        System.out.println("对象初始化之后进行的操作");
    }
    
    @PreDestroy // Java应用需要注册shutdownhook才可以执行对象销毁操作
    public void destroy() {
        System.out.println("对象销毁之前执行的操作");
    }
}
```

另外一种就是使用@Bean注解中的initMethod和destroyMethod属性指定类中的方法来达成

```java
public class ClassOne {
    public void init() {
        System.out.println("对象初始化之后执行的操作");
    }
    public void close() {
        System.out.println("对象销毁之前执行的操作");
    }
}

public class JavaConfig {
    @Bean(initMethod = "init", destroyMethod = "close")
    public ClassOne classOne() {
        return new ClassOne();
    }
}
// 默认在bean对象中定义为close或者shutdown的方法作为对象销毁之前自动执行的方法
// 想要关闭的话，可以将destroyMethod设为""空字符串
// 对于init方法，还有一个替代的实现
public class JavaConfig {
    @Bean(destroyMethod = "")
    public ClassOne classOne() {
        ClassOne classOne = new ClassOne();
        classOne.init();  // 在这个方法中主动调用初始化之后需要执行的方法即可
        return classOne;
    }
}
```

#### bean的作用域

使用@Scope注解来定义bean对象的作用域

```java
@Configuration
@Import
@ComponentScan
public class JavaConfig {
    @Bean
    @Scope("prototype")  // 默认singleton单例作用域
    public ClassOne classOne(){
        return new ClassOne();
    }
}
```

#### bean的名字和别名

默认情况下，获取bean对象的标识就是方法名，还可以通过@Bean标签的name属性来设置

```java
@Configuration
public class JavaConfig {
    @Bean(name = "beanName")  // 为bean设置名字
    public ClassOne classOne() {
        return new ClassOne();
    }
}
// 如果想要设置别名的话，name属性接受字符串数组
// 数组中的字符串都是该bean的别名，可以使用这些名字访问bean对象
// 示例：@Bean(name = {"classOne", "anotherName"})
```

#### bean描述介绍信息

为了给bean对象添加一个描述信息，可以使用@Description注解

```java
@Configuration
public class JavaConfig {
    @Bean
    @Description("这个bean对象主要是为了展示如何使用一些bean所有的配置项")
    public ClassOne classOne() {...}
}
```

### @Configuration使用

@Configuration是类层面的注解，指示这个类作为bean定义的源头容器，@configuration下定义的bean之间还可以作为依赖

#### 注入inter-bean之间的依赖

当bean之间有依赖关系时，表达这个依赖可以简单的通过调用bean定义的方法调用来实现

```java
class ClassOne {
    private ClassTwo classTwo;
    
    public ClassOne(ClassTwo classTwo){
        this.classTwo = classTwo;
    }
}

class ClassTwo {
    ...
}

@Configuration
public class JavaConfig {
    
    @Bean
    public ClassOne classOne() {
        return new ClassOne(classTwo());  // 可以直接通过调用方法的方式注入依赖
    }
    
    @Bean
    public ClassTwo classTwo {
        return new ClassTwo();
    }
}
```

#### lookup方法注入

这个注入是一个很少使用的功能，往往用在singleton作用的类有一个为prototype作用域的依赖的时候，需要使用这个功能，下面的代码将展示如何使用lookup方法注入：

```java
public abstract class CommandManager {
    public Object process(Object CommandState) {
        Command command = createCommand();
        command.setState(commandState);
        return command.execute();
    }
    
    protected abstract Command createCommand();
}
```

```java
@Configuration
public class JavaConfig {
    @Bean
    @Scope("prototype")
    public AsyncCommand asyncCommand(){
        return new AsyncCommand();
    }
    
    @Bean
    public CommandManager commandManager() {
        return new CommandManager() {
            protected Command createCommand() {
                return new AsyncCommand(); // 使用lookup方法注入获取新的prototype的新对象
            }
        }
    }
}
```

### Java注解配置

#### @Import使用

Import注解是用来在当前注解的配置类中导入其他的注解配置类

```java
@Configuration
public class JavaConfig1 {
    ...
}

@Configuration
@Import(JavaConfig.class)  // 使用这个类来导入其他的配置类
public class JavaConfig2 {
    ...
}
```

#### 利用导入的bean中注入依赖

```java
class ClassOne {}

class ClassTwo {
    private ClassOne classOne;
    
    public ClassTwo() {}
    
    public ClassTwo(ClassOne classOne) {
        this.classOne = classOne;
    }
}

@Configuration
public class JavaConfig1 {
    @Bean
    public ClassOne classOne() {
        return new ClassOne();
    }
}

@Configuration
@Import(JavaConfig1.class)
public class JavaConfig {
    @Bean  // 第一种注入不同配置中的bean对象依赖的方法，使用的是类型匹配来注入自动找寻依赖
    public ClassTwo classTwo(ClassOne classOne) {
        return new ClassTwo(classOne);
    }
}

@Configuration
@Import(JavaConfig1.class)
public class JavaConfig {
    @Autowired  // 第二种：使用Autowired和Value注解来完成更进一步的依赖注入，利用上这个两者的优点
    private ClassOne classOne;
    
    @Bean
    public ClassTwo classTwo(){
        return new ClassTwo(classOne);
    }
}
```

#### 为了更简单的找到匹配的导入bean

尽管自动装配带来了很大的方便，但是并不能很方便明确的找到该装配类的定义位置，为了实现这个目的，可以按照下面示例代码来完成：

```java
// 将配置类对象自动装配为这个配置类的依赖
@Configuration
public class JavaConfig{
    @Autowired
    private AnotherConfig anotherConfig;
    
    public ClassTwo classTwo(){
        return new ClassTwo(anotherConfig.classOne());  // 直接使用配置类调用bean注解的方法
    }
}
```

上述的代码因为关系定义相当的明确，可以很清楚的看清这其中的依赖关系，但是若是存在继承或是接口的实现，就比较复杂了，应该使用更加明确的配置类导入：

```java
@Configuration
public class ServiceConfig {

    // 配置类的依赖自动装配
    @Autowired
    private RepositoryConfig repositoryConfig;

    @Bean
    public TransferService transferService() {
        return new TransferServiceImpl(repositoryConfig.accountRepository());
    }
}

@Configuration
public interface RepositoryConfig {

    @Bean
    AccountRepository accountRepository();
}

// 看起来不那么明确的接口实现后的配置类
@Configuration
public class DefaultRepositoryConfig implements RepositoryConfig {

    @Bean
    public AccountRepository accountRepository() {
        return new JdbcAccountRepository(...);
    }
}

@Configuration // 导入时应该导入更加明确的配置类
@Import({ServiceConfig.class, DefaultRepositoryConfig.class})  // import the concrete config!
public class SystemTestConfig {

    @Bean
    public DataSource dataSource() {
        // return DataSource
    }

}

public static void main(String[] args) {
    ApplicationContext ctx = new AnnotationConfigApplicationContext(SystemTestConfig.class);
    TransferService transferService = ctx.getBean(TransferService.class);
    transferService.transfer(100.00, "A123", "C456");
}
```

### Java注解和xml配置结合

#### 以配置文件为主的配置

将Configuration类声明为配置文件的bean对象：

```java
@Configuration
public class JavaConfig {
    @Bean
    public ClassOne classOne() {}
    
    @Bean
    public ClassTwo classTwo() {}
    
    @Bean
    public classThree classThree() {}
}
```

xml配置文件：

```xml
<beans>
    <!-- 基本的容器配置项 -->
	<context:annotation-config />
    <context:property-placeholder location="property-file路径" />
    <context:component-scan base-package="com.jiabao" />
    
    
    <bean class="com.jiabao.JavaConfig" />
    
    <!-- 导入数据库jdbc数据库资源类 -->
    <bean class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <!-- 调用property数据文件中的指定name对应的值 -->
    	<property name="url" value="${jdbc.url}" />
        <property name="username" value="{$jdbc.username}" />
        <property name="password" value="{$jdbc.password}" />
    </bean>
</beans>

<!--
property配置文件中的内容：
jdbc,url=jdbc:mysql://localhost:3306/test
jdbc.username=root
jdbc.password=123456
-->
```

#### Java配置为主的使用

spring中的注解@ImportResource使用来导入xml配置文件的

```java
// 使用注解为主的IOC容器和依赖注入，和前面的xml配置为主实现的内容相同

@Configuration
@ImportResource("xml-file路径")
@ComponentScan("com.jiabao")
public class JavaConfig {
    // 下面的三个字段都使用了@Value注解来实现基本数据类型的依赖注入
    @Value("{$jdbc.url}")
    private String url;
    
    @Value("{$jdbc.username}")
    private String username;
    
    @Value("{$jdbc.password}")
    private String password;
    
    @Bean
    public DataSource dataSource() {
        return new DriverManagerDataSource(url, username, password);
    }
}
```

```xml
<beans>
	<context:property-plcaeholder location="property-file路径" />
</beans>
```

