



## 配置文件

```java
@Component
@ConfigurationProperties(prefix="person")  // 标识以这个前缀为开头的配置文件中的属性和当前配置类进行映射
public class Person {
    private String name;
}
```

需要导入配置文件处理器，这样编写配置才回有提示：

```java
<!‐‐导入配置文件处理器，配置文件进行绑定就会有提示‐‐>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring‐boot‐configuration‐processor</artifactId>
    <optional>true</optional>
</dependency>
```

取值的方式：`@Value`和`@ConfigurationProperties`，两种方式的区别：不管是properties值和yml配置文件都可以获取到值

|                | @ConfigurationProperties | @Value       |
| -------------- | ------------------------ | ------------ |
| 功能           | 批量注入配置文件中的属性 | 需要手动指定 |
| 松散绑定       | 支持                     | 不支持       |
| SpEL           | 不支持                   | 支持         |
| JSR303数据校验 | 支持                     | 不支持       |
| 复杂类型封装   | 支持                     | 不支持       |



- `@ConfigurationProperties`风格

```java
@Component
@ConfigurationProperties(prefix = "person")
@Validated
public class Person {
    /**
    * <bean class="Person">
    * <property name="lastName" value="字面量/${key}从环境变量、配置文件中获取值/#
    {SpEL}"></property>
    * <bean/>
    */
    //lastName必须是邮箱格式，数据校验
    @Email
    //@Value("${person.last‐name}")
    private String lastName;
    //@Value("#{11*2}")
    private Integer age;
    //@Value("true")
    private Boolean boss;
    private Date birth;
    private Map<String,Object> maps;
    private List<Object> lists;
    private Dog dog;
}
```

- `@Value`取值方式

```java
/**
* 将配置文件中配置的每一个属性的值，映射到这个组件中
* @ConfigurationProperties：告诉SpringBoot将本类中的所有属性和配置文件中相关的配置进行绑定；
* prefix = "person"：配置文件中哪个下面的所有属性进行一一映射
*
* 只有这个组件是容器中的组件，才能容器提供的@ConfigurationProperties功能；
* @ConfigurationProperties(prefix = "person")默认从全局配置文件中获取值；
*
*/
@PropertySource(value = {"classpath:person.properties"})
@Component
@ConfigurationProperties(prefix = "person")
//@Validated
public class Person {
    /**
    * <bean class="Person">
    * <property name="lastName" value="字面量/${key}从环境变量、配置文件中获取值/#
    {SpEL}"></property>
    * <bean/>
    */
    //lastName必须是邮箱格式
    // @Email
    //@Value("${person.last‐name}")
    private String lastName;
    //@Value("#{11*2}")
    private Integer age;
    //@Value("true")
    private Boolean boss;
}
```

- `@ImportResouurce`：注解使用，导入spring的配置文件

```java
@ImportResource(locations = {"classpath:beans.xml"})
// 导入Spring的配置文件让其生效
```

xml配置文件

```java
<?xml version="1.0" encoding="UTF‐8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema‐instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring‐beans.xsd">
    <bean id="helloService" class="com.atguigu.springboot.service.HelloService"></bean>
</beans>
```

- `@Configuration`用来将这个java标注为配置文件，然后使用@Bean注解标识当前为Bean对象，交给spring管理

```java
/**
* @Configuration：指明当前类是一个配置类；就是来替代之前的Spring配置文件
* 在配置文件中用<bean><bean/>标签添加组件
*
*/
@Configuration
public class MyAppConfig {
    //将方法的返回值添加到容器中；容器中这个组件默认的id就是方法名
    @Bean
    public HelloService helloService02(){
    System.out.println("配置类@Bean给容器中添加组件了...");
    return new HelloService();
    }
}
```

### Profile多配置文件

主配置文件可以是：`application-{profile}.properties/yml`，默认使用的配置文件`application.yml/properties`

yml文件指定多问挡块方式：

```yaml
server:
	port: 8081
spring:
	profiles:
		active: prod
‐‐‐
server:
	port: 8083
spring:
	profiles: dev
‐‐‐
server:
	port: 8084
spring:
	profiles: prod #指定属于哪个环境
```

或者多配置文件使用：

`application-dev.yml`、`application-prod.yml`，分别配置好内容即可

激活profile的方式：

- 配置文件中指定：`spring.profiles.active=dev`
- 命令行：`java -jar xxx.jar --spring.profiles.active=dev`
- 虚拟机参数：`-Dspring.profiles.active=dev`



### 配置文件加载位置

扫描配置文件的路径位置：优先级从高到低，高优先级的配置文件会覆盖低优先级的配置

- `-file:./config/`
- `-file:./`
- `-classpath:/config/`
- `-classpath:/`

改变配置文件路径：`spring.config.location`配置文件中的改配置项改变配置文件路径



### 自动配置原理

1. springboot启动的时候加载主配置类XxxApplication，主配置类开启了自动配置功能`@EnableAutoConfiguration`

2. `@EnableAutoConfiguration`作用：

   - `@EnableAutoConfigurationImportSelector`给容器中导入组件
   - 查看`selectImports()`方法中的内容
   - `List configurations = getCandidateConfigurations(annotationMetadata, attributes);`获取候选的配置（加载定义在spring.factories文件中的配置类）
   - 扫描jar包路径下的内容：`META‐INF/spring.factories`。一般都是`XxxxAutoConfiguration`的形式的自动配置类，用这些类来做自动配置

   ```java
   SpringFactoriesLoader.loadFactoryNames();   // 容器加载器将配置工厂加载进来
   /**
   扫描所有jar包类路径下 META‐INF/spring.factories
   把扫描到的这些文件的内容包装成properties对象org.springframework.boot.autoconfigure.EnableAutoConfiguration=\后面设置的class全路径就是会被扫描自动配置的类。这个是交给SpringFactoriesLoader类来检索加载配置的
   从properties中获取到EnableAutoConfiguration.class类（类名）对应的值，然后把他们添加在容器中*/
   ```

3. 利用上面加载的`XxxAutoConfiguration`或者配置类做自动配置

4. 下面用`HttpEncodingAutoConfiguration`作为示例：

```java
@Configuration //表示这是一个配置类，以前编写的配置文件一样，也可以给容器中添加组件

@EnableConfigurationProperties(HttpEncodingProperties.class) //启动指定配置类的
// ConfigurationProperties功能；将配置文件中对应的值和HttpEncodingProperties绑定起来
// 并把HttpEncodingProperties加入到ioc容器中

@ConditionalOnWebApplication
//Spring底层@Conditional注解（Spring注解版），根据不同的条件，如果满足指定的条件，整个配置类里面的配置就会生效； 判断当前应用是否是web应用，如果是，当前配置类生效

@ConditionalOnClass(CharacterEncodingFilter.class) //判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；

@ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing=true)
//判断配置文件中是否存在某个配置 spring.http.encoding.enabled；如果不存在，判断也是成立的
//即使我们配置文件中不配置pring.http.encoding.enabled=true，也是默认生效的；
public class HttpEncodingAutoConfiguration {
    // 已经和SpringBoot的配置文件映射了
    private final HttpEncodingProperties properties;
    // 只有一个有参构造器的情况下，参数的值就会从容器中拿
    public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) {
    this.properties = properties;
    }
    @Bean //给容器中添加一个组件，这个组件的某些值需要从properties中获取
    @ConditionalOnMissingBean(CharacterEncodingFilter.class) //判断容器没有这个组件？
    public CharacterEncodingFilter characterEncodingFilter() {
        CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
        filter.setEncoding(this.properties.getCharset().name());
        filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
        filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
        return filter;
    }
}
```

自动配置的关键：

- SpringBoot的启动会加载大量的自动配置类`XxxAutoConfiguration`
- 先查看是否有该功能的自动配置
- 然后查看自动配置类里面是否已经加载配置过我们想要使用的功能，这样就可以省略很多的配置时间
- 给容器中自动配置类添加组件时，会从properties类中加载获取某些属性，这些属性往往是和配置文件中映射的，我们可以在配置文件指定这些属性的值

#### Conditonal注解

`@Conditional`注解用来指定添加组件的条件，只有当条件成立才向容器中添加组件，配置的内容才生效

![image-20220103180616361](https://gitee.com/Jia_bao_Li/img/raw/master/img/Conditional%E6%B3%A8%E8%A7%A3%E9%85%8D%E7%BD%AE%E5%86%85%E5%AE%B9.png)

在配置文件中设置debug=true可以用来查看哪些自动配置成功（Positive），哪些失败（Neaative）



#### spring.factories作用

1. 在spring-boot项目中pom文件里面添加的依赖中的bean需要注册到spring-boot项目的spring容器。由于`@ComponentScan`注解只能扫描spring-boot项目包内的bean并注册到spring容器中，因此需要`@EnableAutoConfiguration`注解来注册项目包外的bean。而spring.factories文件，可用来记录项目包外需要注册的bean类名。然后利用`AutoConfigurationSelector`类的`selectImports()`方法：该方法下的`getAutoConfigurationEntry()`获取的是springboot项目中需要自动配置的bean。下面的`getCandidateConfiguration()`会利用`SpringFactoriesLoader`的`loaderFactoryName()`去加载`spring.factories`文件配置的bean
2. 提供SDK或者Spring Boot Starter给被人使用时，让使用者只需要很少或者不需要进行配置，然后在服务中引入我们的jar包即可

