# MALL项目

## 1 项目组织结构

```bash
jbmall
├── jbmall-common  -- 工具类及通用代码，其他的服务所依赖的公共类，大部分的工具类已经共同依赖在这里导入
├── jbmall-security -- SpringSecurity封装公用模块
├── jbmall-admin    -- 后台商城管理系统接口，基于人人快速开发代码平台修改的
├── jbmall-search   -- 基于Elasticsearch的商品搜索系统
├── jbmall-product  -- 商品服务
├── jbmall-member   -- 会员服务
├── jbmall-ware     -- 库存服务
├── jbmall-order    -- 订单服务
├── jbmall-coupon   -- 优惠券服务
├── jbmall-portal   -- 前台商城系统接口
├── jbmall-thirdparty   -- 商城接入第三方的一些功能或者API的位置，主要管理这一部分内容
└── jbmall-demo     -- 框架搭建时的测试代码
```

## 2 项目功能

- 商品模块

  - 商品管理
  - 商品分类管理
  - 商品类型管理
  - 品牌管理

- 订单模块

  - 订单管理
  - 订单设置
  - 退货申请处理
  - 退货原因设置

- 营销模块

  - 秒杀活动管理
  - 优惠价管理
  - 品牌推荐管理
  - 新品推荐管理
  - 人气推荐管理
  - 专题推荐管理
  - 首页广告管理

- 会员模块

- 库存模块

  

## 3 技术选型

### 后端技术

认证和授权：SpringSecurity

技术框架：SpringBoot

ORM框架：Mybatis

数据层生成器：Mybatis-generator

搜索引擎：ElasticSearch

消息队列：RabbitMQ

分布式缓存：Redis

数据库：MySQL（主要业务数据存储）、MongoDB（用户行为分析数据存储）、Redis（缓存）

日志收集：LogStash

数据库连接池：Druid

对象存储：OSS、MinIO

登录支持：JWT

简化对象封装工具，用来简化对象封装的流程，不用再去手动的在IDEA敲击生成，只需要使用注解标识，编译时就会自动生成相应的代码：Lombok

Java工具类库：Hutool

分页工具：PageHelper

验证框架：Hibernator-Validator

### 前端技术

VUE+ElementUI搭建

## 4 微服务

### 4.0 技术搭配

注册中心：SpringCloud Alibaba-Nacos

配置中心：SpringCloud Alibaba-Nacos

负载均衡：SpringCloud Ribbon

远程调用（声明式远程调用服务）：SpringCloud-Feign

服务容错（降级、限流、熔断）：SpringCloud Alibaba Sentinel

API网关：SpringCloud Gateway

调用链监控：SpringCloud Sleuth

分布式事务解决方案：SpringCloud Alibaba Seata：原来是fescar

![image-20211122140820151](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E5%95%86%E5%9F%8E%E5%BE%AE%E6%9C%8D%E5%8A%A1%E6%9E%B6%E6%9E%84%E5%9B%BE.png)



![image-20211122141500558](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%AD%E5%BF%83%E3%80%81%E9%85%8D%E7%BD%AE%E4%B8%AD%E5%BF%83.png)

注册中心是用来将各个微服务注册到一个统一的管理平台，方便后期的管理监控以及调度等

配置中心是用来快速修改某个服务的配置而不去服务所在的服务器上去修改配置重新部署，方便了业务的更新上线

网关用来过滤请求，对于恶意请求可以进行处理丢弃

注册中心还可以起到负载均衡的作用，查看哪个服务器上的哪一个服务调用比较少就可以调度这个服务器进行服务的相应处理

![image-20211122141415693](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E4%B8%9A%E5%8A%A1%E9%80%BB%E8%BE%91%E6%9E%B6%E6%9E%84%E5%9B%BE.png)



### 4.1 微服务的注册中心

一般注册中心和配置中心可以使用Nacos作为服务注册、配置管理

服务注册的流程：

- 引入nacos的discovery发现注册的依赖项

- 在配置文件application.yml中添加配置项：

  ```yml
  spring:
    datasource:
      username: root
      password: root
      url: jdbc:mysql://127.0.0.1:3303/jbmall_pms?useSSL=false
      driver-class-name: com.mysql.jdbc.Driver
    # 加入这一部分注册发现的配置项
    cloud:
      nacos:
        discovery:
          server-addr: 127.0.0.1:8848    # 指定nacos发现注册的服务器地址和端口号
  
  mybatis-plus:
    mapper-locations: classpath:/mapper/**/*.xml
    global-config:
      db-config:
        id-type: auto
  
  ```

- 在服务的启动类上加一个注解开启自动发现@EnableDiscoveryClient

### 4.2 配置中心

还可以继续使用nacos来做配置中心

配置中心的设置步骤：

- 首先加入nacos 的配置的依赖项

- 添加配置中心的配置文件bootstrap.properties

  ```properties
  application.name=jbmall-product
  spring.cloud.nacos.config.server-addr=127.0.0.1:8848
  ```

- 在业务逻辑层上面加一个注解@RefreshScope，表示会刷新配置文件内容并加载

  ```java
  @RefreshScope
  public class SampleController {
      @Value("${product.label.name}")
      String label;
      @Value("${product.price.number}")
      double price;
  }
  ```

- 之后需要修改配置文件内容时可以发起post请求，也可以直接在nacos服务端里面修改对应服务的默认配置文件`服务名.properties`，这样就可以做到配置文件实时刷新

配置中心的细节：

- 命名空间：环境隔离（开发环境，测试环境和生产环境隔离）要使用特定命名空间下的配置文件就可以在bootstrap.properties中加入一行配置即可，设置命名空间，需要选择特定的一串字符串的值。也可以为不同的服务创建各自的命名空间

  ```properties
  spring.application.name=jbmall-product
  spring.cloud.nacos.server-addr=127.0.0.1:8848
  spring.cloud.nacos.config.namespace=1889sfanjkb-dajva
  spring.cloud.nacos.config.group=dev
  ```

- 配置集：所有配置的集合

- 配置集ID：类似文件名

- 配置分组：默认所有的配置集都书用户DEFAULT_GROUP。在bootstrap.properties新增一个group的配置项设置指定的配置分组



- 创建配置bootstrap.properties，设置服务配置中心配置文件

- ```properties
  spring.cloud.nacos.config.namespace=
  spring.cloud.nacos.config.group=
  ```

多配置集

主要的配置文件：`bootstrap.properties`设置说明加载哪些配置文件

```properties
spring.cloud.nacos.config.extension-configs  # 这个配置项是一个List类型，可以配置多个配置文件写入

spring.cloud.nacos.config.ext-config[0].data-id=datasource.yml
spring.cloud.nacos.config.ext-config[0].group=dev
spring.cloud.nacos.config.ext-config[0].refresh=true

spring.cloud.nacos.config.ext-config[1].data-id=mybatis.yml
spring.cloud.nacos.config.ext-config[1].group=dev
spring.cloud.nacos.config.ext-config[1].refresh=true
```

### 4.3 API网关

是流量的入口，常用的功能：

- 路由转发
- 权限校验
- 限流控制

使用的是springCloud gateway网关

步骤：

- 导入依赖

- 创建配置文件application.yml，设置服务注册发现

- ```yaml
  spring: 
  	cloud: 
  		nacos: 
  			discovery: 
  				serve-addr: 127.0.0.1:8848
  
  # 配置数据源
  spring:
  	datasource: 
  		username: root
  		password: root
  		url: jdbc:mysql://127.0.0.1:3306/jbmall_pms?useSSL=false
  		driver-class-name: com.driver.jdbc.Driver
  		
  
  ```

  

- 网关的配置项：

  ```yaml
  spring: 
  	cloud:
  		gateway: 
  			routes:
  				- id: test_route
  				uri: https://www.baidu.com
  				predicates: 
  					- Query=url,baidu
  				- id: qq_rooute
  				uri: https://www.qq.com
  				predicates: 
  					- Query=url,qq
  				# 配置路由的负载均衡，将请求转发到这个服务上
                  - id: admin_route
                    uri: lb://jbmall-product
                    predicates:
                      - Path=/api/**    # 转发的路由规则，包含api的就转发到这个服务
                    # 过滤器路径重写功能
                    # 将原路径重写为/**根目录下的任何内容的访问
                    filters:
                      - RewritePath=/api/(?<segment>.*),/$\{segment}   # rewrite path
  # 上述的配置项配置好之后就可以启动gateway服务，然后利用gateway服务来过滤请求转发请求
  ```


网关配置跨域请求：

跨域：指的是浏览器不能执行其他网站的脚本，浏览器的同源策略造成的，浏览器对JavaScript施加的安全限制

同源策略：指协议、域名和端口都要相同，其中任何一个不同都会产生跨域

```java
// 网关配置跨域需创建配置类来实现

// MyCorsConfiguration.java

package com.jiabao.jbmall.gateway.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.reactive.CorsWebFilter;
import org.springframework.web.cors.reactive.UrlBasedCorsConfigurationSource;


// 如果跨域不成功，可能是在后台管理中也有一层跨域配置
import java.util.Collections;

@Configuration
public class MyCorsConfiguration {

    @Bean
    public CorsWebFilter corsWebFilter() {
        UrlBasedCorsConfigurationSource urlBasedCorsConfigurationSource = new UrlBasedCorsConfigurationSource();
        CorsConfiguration corsConfiguration = new CorsConfiguration();

        // 配置跨域
        corsConfiguration.addAllowedHeader("*");
        corsConfiguration.addAllowedMethod("*");
        corsConfiguration.addAllowedOrigin("*");
        corsConfiguration.setAllowCredentials(true);
        urlBasedCorsConfigurationSource.registerCorsConfiguration("/**",corsConfiguration);
        return new CorsWebFilter(urlBasedCorsConfigurationSource);
    }
}
```



### 4.4 openfeign远程调用

步骤：

- 引入openfeign依赖

- 编写一个接口，告诉springcloud这个接口需要调用远程服务

  ```java
  @FeignClient("jmall-coupon")  // 参数传入远程服务名
  public interface CouponService {
      // 写上需要调用的功能完整签名写过来
      @RequestMapping("/coupon/coupon/member.list")
      public R membercoupons();
  }
  ```

- 开启远程调用

  ```java
  // 远程调用功能在服务的启动类加上即可具备远程调用功能
  // 在基础包里面扫描远程调用功能
  @EnableFeignClient(basePackages="com.jiabao.jbmall.member.feign")
  @EnableDiscoveryClient
  @SpringBootApplication
  public class JbmallMemberApplication {
      public static void main(String[] args) {
          SpringApplication.run(JbmallMemberApplication.class);
      }
  }
  ```

- 然后就可以在其他需要调用远程接口的业务层里面自动注入远程服务接口，调用远程服务即可



## 5 前端开发基础

ES6是前端浏览器脚本语言的一个规范，而JavaScript是具体的实现。

### 5.1 ES的一些语言特性

```javascript
// 变量定义相关的一些特性

let a = 2;
var b = 3;
const c = 5;

// 注意的是，var声明的变量存在变量提升，后面定义的变量可以在前面使用，let不可以
// var还存在变量跨域的问题
{
    let a = 3;
    var b = 4;
}
// 代码块外边还可以访问到b但是不可以访问到a
// var定义的变量可以重复定义声明那个，但是let的就不可

// const是用来生命只读变量，也就是常量
```

结构表达式

```javascript
let a = [1,2,3];

let a = arr[0];
let b = arr[1];
let c = arr[2];

// 和上面步骤操作结果一样
// 数组解构
let [a,b,c] = arr;


const person =  {
    name: "jacson",
    age: 21,
    language: ['java','python','js']
}

// 解构对象
const {name,age,language} = person;
// 对象名换名字结构
const {name:ab,age,language} = person;
```

字符串扩展API

```javascript

let name = "hello";
// 几个常用好用api
console.log(name.startsWith("hel"));
console.log(name.endsWith("lo"));
console.log(name.includes("e"));
// 字符串模板和多行字符串
let ss = `
vaejfg 
		jifnklanf
	fakhefuife
`;
console.log(ss);

// 格式化字符串和变量传入,也可以使用表达式，此外还可以调用方法获取方法的返回值
let info = `my name is ${name},i am ${age} old`;

```

函数部分：

```javascript
// 传统的函数创建
function add(a,b) {
    b = b || 1;  // 判断b是否为空，为空就返回1，短路操作符
    return a+b;
}
// 传入默认值
function add(a,b=1) {
    return a+b;
}
// 不定参数
function fun(...values) {
    console.log(values.length);
}
// 箭头函数
var print = obj => console.log(obj);
var print = function(obj) {console.log(obj);};

// 箭头函数+解构表达式
var hello = ({name}) => console.log("hello"+name);
hello(person);
```

对象的新增API优化

```javascript
// 箭头函数不能使用this关键字访问对象的属性

const person =  {
    name: "jacson",
    age: 21,
    language: ['java','python','js']
}

console.log(Object.keys(person));     // 返回属性名称
console.log(Object.values(person));   // 返回属性对应的值
console.log(Object.entries(person));  // 返回对象中所有的项目

// 合并对象
Object.assign(target,person1,person2);

// 声明对象简写
let name = "jiabao";
let age = 23;
let person = {
    age:age,name:name
};
// 对象函数属性简写
let person3 = {
    name: "jiabao",
    eat: function(foot) {
        console.log(this.name + "eating" + foot);
    },
    eat2: (food) => console.log(person3.name+ "eating" + food),
    eat3(food){
        console.log(person3.name+ "eating" + food);
    }
}

// 对象扩展符
// 对象的深拷贝
const person =  {
    name: "jacson",
    age: 21,
    language: ['java','python','js']
};
const someone = {...person};   // 将person深拷贝过来
// 合并对象
const coutry = {
    coutry: "China"
};
// 存在重复时后面的会覆盖前面的值
const newPerson = {...person,...coutry};
```

数组函数

```javascript
// 数值函数map，将数组中的所有元素都进行某个操作之后放入新数组返回
let arr = [1,3,5,9,7];
arr =  arr.map((item)=>{
    item*2/5
})

// 数组函数reduce，将数组的相邻两个元素进行某种处理

arr = arr.reduce((a,b)=> {
    return a+b;
});
// 求前缀和
```

promise异步操作

```javascript
// 解决回调嵌套问题
let p = new Promise(()=> {
    $.ajax({
        url: "mock/user.json",
        success: function (data) {
            console.log("查询用户成功":data);
            resolve(data);
        }
        error: function(err) {
            reject(err);
        }
    })
});

p.then((obj)=> {
    return new Promise((resolve,reject) => {
        $.ajax({
            url: `mock/user_corse_${obj.id}.json`,
            success: function(data) {
                console.log("查询用户课程成功",data);
                resolve(data);
            },
            error: function(err) {
            	reject(err);
            }
        });
    })
}).then((data)=> {
    console.log("上一步的结果",data);
    $.ajax({
        url: `mock/course_score_${data.id}.json`,
        success: function(data) {
            console.log("查询课程得分成功",data);
        },
        error: function(err){}
    });
});

// 上面代码的封装优化
function get(url,data) {
    return new Promise((resolve,reject) => {
        $.ajax({
            url: url,
            data: data,
            success: function(data) {
                resolve(data);
            },
            error: function(err) {
                reject(err);
            }
        })
    });
}
// 封装嵌套，代码简洁明了
get("mock\user.json").then((data) => {
    console.log("用户查询成功");
    return get(`mock/user_course_${data.id}.json`);
})
.then((data) => {
    console.log("课程查询成功：",data);
    return get(`mock/course_score_${data.id}.json`);
})
.then((data) => {
    console.log("课程成绩查询成功",data);
})
.catch((err) => {
    console.log("出现异常",err);
});
```

模块化：

```javascript
// 类似于java的导包

// hello.js
function hello({name,age,language}) {
    console.log("hello",name,age);
    console.log(language);
}

export {hello};

// user.js
const person =  {
    name: "jacson",
    age: 21,
    language: ['java','python','js']
}

export {person};

// main.js

import {person} from "./user.js";
import {hello} from "./hello.js";

hello(person);

// 如果想要使用模块化功能在hmtl文件中，在导入js文件时需要指定type="module"
// 还有在导入模块的时候也需要使用{}将导入的内容包裹起来
```

### 5.2 Vue

#### 5.2.1 MVVM思想

- M：model，模型，包含数据的一些基本操作
- V：view视图，只要model发生改变视图也需要改变，view改变，数据也变化
- VM：view-model

#### 5.2.2 Vue的简单使用

- npm是前端的包管理工具：`npm init -y`初始化vue项目
- `npm install vue`安装vue.js
- 导入vue.js：`<script src="./node_modules/vue/dist/vue/js"></script>`
- 创建Vue实例，关联页面的某些内容模板，将数据渲染到关联的模板
- 使用指令来简化对标签的某些操作
- 生命方法可以做一些更复杂的操作，将这些声明方法放在methods数据

```javascript
let vm = new Vue({
    el: "",      // 管控元素，通过css选择器进行匹配控制
    data: {
        name: "jiabao",         // 数据区，用来进行读取
    }
});

// 可以实现数据变化页面跟着变化
// 页面变化数据也会跟着变化
```

双向绑定：视图模型和数据同时变化

绑定语法：`v-xxx="数据区"`，`v-on:click="num++"`，还有事件绑定

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    
    <div id="person_name">
        <!实现双向绑定到vm的数据区和某个元素,使用v-model指向对应的数据>
        <input type="text" v-model="num">
        <button v-on:click="num++">
            点赞
        </button>
        <button v-on:click="cancel">
            取消
        </button>
        <h1>{{name}},非常不错，有{{num}}个人点赞</h1>
    </div>
    <script src=./node_modules/vue/dist/vue.js></script>
    <script>
        let vm = new Vue({
            el: "#person_name",
            data: {
                name: "jiabao",
                num: 5
            },
            methods: {
                cancel() {
                    this.num --;
                }
            }
        })
    </script>
</body>
</html>
```

vue实例中常用的几个对象属性：

- `el`：用来关联绑定元素
- `data`：用来放数据的数据区
- `methods`：用来防止复杂操作方法的地方

vue在html标签中的一些指令集：

- `v-html`和`v-text`：前者不会对htm标签进行转义，会直接将其作为html标签渲染，后者则会转移，当成字符串输出。单向绑定
- `v-bind`：将标签绑定到数据区的数据`<h1 v-bind:href="link">百度网址</h1>`，v-bind之后可以使用js的数据。单向绑定 ，简写`:`表示`v-bind`

插值表达式：`{{}}`，只可以在标签体内部使用，而使用指令的时候不可以，只能够使用Vue实例的属性名

想要在标签体上使用，可以使用指令`v-bind`



```html
<! for循环语句，循环遍历集合内的所有元素 >
<! 不仅可以遍历集合，还可以遍历对象的元素 >
<! v-if 用来当条件为真是显示标签 >
<! v-show条件真显示，但是标签是一直存在的 > 
<div id="app">
<li v-for="(user,index) in users">
    {{user.name}} => {{user.gender}} => {{user.age}}
</li>
</div>
<script>
	let vm = new Vue({
        el: "#app",
        users:[{},{},{}]
    })
</script>
```

```html
<script>
	let vm = new Vue({
        el: "#app",
        users:[{},{},{}],
        computed: {
            totalprice(){
                return price;
            },
        },
        // watch 用来监控一个值的变化，从而根据值得变化作出相应的反应 >
        watch: {
            totalprice(newVal,oldVal) {
                // 这里可以定义函数监控值变化时进行的一些处理步骤
            }
        }
    })
</script>
```

过滤器

```html
<script>
	let vm = new Vue({
        el:"#app",
        filters:{
            // 创建过滤器函数，进行传入的值过滤
            genderFilter(val) {
                if (val==1) return "男";
                else return "女";
            }
        }
    });
    Vue.filter("gFilter",function(val) {
		if (val==1) return "男";
        else return "女";
    });
</script>
```

组件化：大型应用，页面可以由很多部分组成，但是不同页面往往可能存在重复的部分，将页面的开发分成多个不同的部分来进行开发，降低重复开发。这种分部分开发的方式成为组件化开发

Vue里面，所有的vue实例都是组件

```html
<script>
	// 全局声明一个组件,全局可用
    Vue.component("组件名",{
        template: `<button v-on:click="cout++">我被点击了{{count}}次</button>`,
		data() {
			return {
				count: 1
			}
		}
    });
	// 局部创建组件
    // 1. 创建组件对象
    const buttonCounter = {
        template: `<button v-on:click="cout++">我被点击了{{count}}次</button>`,
		data() {
			return {
				count: 1
			}
		}
    });
    // 2. 将组件加入到Vue实例对象的conponents标签里面
    new Vue({
        el: "#app",
        data:{
            cout:1
        },
        components: {
            'button-counter': buttonCounter
        }
    });
    // 然后就可以在app的标签内容里面使用这样的组件对象
</script>
<组件名></组件名>    <! 组件的使用，当成简单的html标签来使用>
```

vue的单文件组件开发：

```html
// 命名 xxx.vue
// 单文件组件开发的三部分

// 1：template
<template>
	<p> {{ greeting }} World! </p>
</template>

// 2：script
<script>
	module.exports = {
        data: function () {
            return {
                greeting: "hello"
            }
        }
    }
</script>

// 3:style风格,css相关内容
<style scoped>
    p {
        font-size: 2em;
        text-align: center;
    }
</style>
```

生命周期和钩子函数：

Vue为生命周期的每个状态都设置了钩子函数（监听函数）。每当Vue实例处于不同的生命周期，对应的函数就会触发

生命周期：

```js
let app = new Vue({
    el: "#app",
    data: {
        name: "zhangsan",
        num: 100
    },
    methods: {
        show(){return this.num;},
        add(){num++;}
    },
    beforeCreate() {
        // 数据模型、方法加载之前
        console.log("生命周期的钩子函数beforeCreate");
    },
    created: function() {
        // 数据模型、方法加载好之后
        console.log("生命周期的钩子函数created");
    },
    beforeMount() {
        // beforeMount的钩子函数，html标签渲染（将数据放入到标签内部）之前
    },
    mounted() {
        // mounted的钩子函数定义，html标签渲染完毕
    },
    beforeUpdate() {
        // 数据更新前的操作，就是数据在后台更新，但是前端标签内部没有更新
    },
    updated() {
        // 数据更新后的钩子函数，数据完全更新完毕
    }
})
```

#### 5.2.3 Vue模块化开发

1. `npm install -g webpack`全局安装打包工具

2. `npm install -g @vue/cli-init`，全局安装vue的脚手架
3. 初始化vue项目：`vue init webpack appname`利用脚手架并借助webpack模板初始化了一个appname的项目
4. 启动vue项目：
   项目的package.json文件中有script，代表可以运行的命令
   `npm start`//也可以使用`npm run dev`启动项目
   项目打包：`npm run build`

路由视图：`route-view`管理页面跳转，根据不同的页面url跳转对应的路由

```html
<! index.html>
  <div id="app">
    <img src="./assets/logo.png">
    <! 路由视图 管理页面跳转内容渲染>
    <router-view/>
    <! route-link to="制定需要跳转的路由">
    <route-link to='/hello'>这里可以设置跳转的路由视图的path</route-link>
  </div>
```

```js
// route路由文件

import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import Hello from '@/components/Hello'
import User from '@/components/User'

// 导入组件库需要使用这个语句使用组件库
Vue.use(Router)

// 在路由文件中需要将路由模块导出，以便其他的模块中可以导入使用，使用default导出的话可以自定义名字导入
export default new Router({
  routes: [
    {
      // 表示在访问根目录下的时候，访问helloWorld组件进行内容的渲染
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      // 访问、hello，跳转Hello组件渲染内容
      path: '/hello',
      name: 'hello',
      component: Hello
    },
    {
        // 还可以设置动态路由匹配，动态匹配上的一类路由都会到这个组件来渲染
        path: '/user/:id',
        name: 'user',
        component: User
    }
  ]
})

```

#### 5.2.4 Vue整合ElementUI

首先需要下载elementUI组件库，然后就可以去官网查找你想要使用的组件，并将其组件内容自己写入到`组件名.vue`

之后，设置好你需要使用的组件加入即可



## 6 业务层逻辑

通过一个三级菜单的业务逻辑的增删改产来熟悉业务的流程。

主要是涉及到数据库的增删改查部分

不同的数据库实体有着不同的数据库操作，也可以自己手写业务逻辑代码

```java
// controller中的一部分操作代码，在实体类中增加了一个数据库中不存在的字段，children
   @Override
    public List<CategoryEntity> listTree() {
        List<CategoryEntity> categoryEntities = categoryDao.selectList(null);
        List<CategoryEntity> menuLevel1 = categoryEntities.stream().
                filter(categoryEntity -> categoryEntity.getParentCid() == 0).
                map(entity -> {
                    entity.setChildren(getChildren(entity,categoryEntities));
                    return entity;
                }).
                sorted((entity1,entity2) -> (entity1.getSort()==null?0:entity1.getSort())-(entity2.getSort()==null?0:entity2.getSort())).
                collect(Collectors.toList());
        return menuLevel1;
    }

    @Override
    public void removeById(Long[] catIds) {

    }

	// 获取数据库中的满足条件的子分类
    public List<CategoryEntity> getChildren(CategoryEntity root,List<CategoryEntity> entities) {
        List<CategoryEntity> children = entities.stream().
                filter(entity -> entity.getParentCid() == root.getCatId()).
                map((entity) -> {
                    entity.setChildren(getChildren(entity, entities));
                    return entity;
                }).
                sorted((entity1,entity2) -> (entity1.getSort()==null?0:entity1.getSort())-(entity2.getSort()==null?0:entity2.getSort())).collect(Collectors.toList());
        return children;
    }

// 一般使用了生成器生成的基本业务逻辑都已经是具备了的
// 比如常用的增删改查list这一类的业务
// 有特殊的复杂需求可能就需要自己来手写并改善性能

// 实体类中增加的字段属性
	// 不是数据表里面的内容需要设置这个注解并且设置exist为false
	@TableField(exist = false)
	private List<CategoryEntity> children;
```

### 6.0 各种类型的Object

VO（View Object）：视图对象，用于展示层，作用就是将某个指定页面的所有数据封装起来，一般是向渲染引擎传输数据的对象。其实也可以理解为前后端交互的一类对象

TO（transfer object）：传输对象，应用程序不同关系之间传输的对象

DTO（data transfer object）：数据传输对象，展示层和服务层之间的数据传输对象

PO（persistent object）：持久化对象，跟持久层的数据库形成一一对应的对象，就是常用的实体对象和数据库进行映射，这样的一类对象大都可以采用自动生成的代码来自动生成

POJO（plain ordinary java object）：简单无规则的java对象，其实就是只有getter和setter方法的一类java实体对象，实质上PO也可以看做POJO的一种类型

DO（domain object）：领域对象，从现实世界抽象出来的业务实体对象

DAO（data access object）：和数据库打交道的一类对象，主要是读写数据库的对象，也是可以自动生成

VO和DTO两者都是前后端交互时使用的对象，二者的区别就是DTO是服务层需要接受的数据和返回的数据，而VO是展示层其实也可以说是前端所需要用来展示的数据对象

VO和DTO可以合并：

- 

VO和DTO并存：

- 

一般只要DO和DTO，一个和数据库打交道，一个是controller和前端打交道

业务逻辑实现的时候先仔细分析一下需要交换或者封装的数据，然后根据所需要的数据创建对应的对象来封装这些数据，也就是上面提到的很多种的object里面去选择合适的。

分析好了封装数据的对象之后，就进行数据库的查询或者是redis缓存的查询获取到对应的数据去进行业务逻辑的实现。

来数据库查询的时候，如果两者查询之间没有什么逻辑联系，就可以选择异步执行，让整个的业务更快的完成。如果需要等待某个任务的结果，也是可以使用异步任务，只是在编排上需要选择等待上一个任务的执行完成即可。异步任务一定要等待所有的任务完成。

### 6.1 删除和逻辑删除

物理删除：是指删除之后整个数据库的关于这条记录的项目就都没有了

逻辑删除：采用数据库的某一个字段来标识记录是否被删除了，这个只是将数据库中的某个字段改变来标识删除与否，也就是采用一个标志位记录这些数据的删除与否，逻辑删除在开发期间主要使用的方法，防止误删或者错删数据

mybatis就支持逻辑删除：

1. 配置全局的逻辑删除规则

   ```yaml
   mybatis-plus:
     global-config:
       db-config:
         logic-delete-field: flag  # 全局逻辑删除的实体字段名(since 3.3.0,配置后可以忽略不配置步骤2)
         logic-delete-value: 1 # 逻辑已删除值(默认为 1)
         logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
   ```

2. 低版本之前还需要设置逻辑删除组件bean（可省略）

3. 在实体类需要进行逻辑删除的字段进行@TableLogic的注解

   ```java
   // 在注解里面可以设置逻辑删除和逻辑不删除
   // value标识逻辑未删除，delval标识逻辑已删除
   @TableLogic(value="1",delval='0')
   private Integer deleted;
   ```

按照上述配置好之后，就只会进行更新字段值为删除的标识值

`删除  ->  更新`

### 6.2 前端和后端数据的校验

前端先做一个表单的校验，后端需要在服务器中写上服务器的校验规则

后端校验：JSR303，`javax.validation.constraint`注解你需要校验的字段

开启校验需要使用`@Valid`注解传输过来的数据

`@Pattern`可以自定义正则表达式的规则

JAR 303使用过程：

1. 使用`javax.validation.constraints`提供的注解来标注需要进行校验的字段

2. 可以在注解处`@NotNull(message="")`自定义验证不合法时返回的默认消息，在一个配置文件`ValidationMessage.properties`中有这些不同注解的默认消息。如果对这些注解消息不满意，你可以自己定义自己喜欢的返回消息，也可以自己在这个配置文件中加入默认信息

3. 在需要进行数据校验的数据处使用上`@Valid`注解标注这个数据需要进行字段的合法性校验

4. 如果需要校验的规则在提供的功能里面不存在，还可以使用`@Pattern(regexp="",message="")`注解自定义正则表达式来匹配，满足的就合法，不满足表示字段非法

5. 如果想要获取校验的结果，需要在`@Valid`注解后面加上一个参数`BindingResult`，利用这个参数可以回去到出错的信息

   ```java
   // 一个开启校验功能的entity
   package com.jiabao.jbmall.product.entity;
   
   import com.baomidou.mybatisplus.annotation.TableId;
   import com.baomidou.mybatisplus.annotation.TableName;
   
   import java.io.Serializable;
   import java.util.Date;
   import lombok.Data;
   import org.hibernate.validator.constraints.URL;
   
   import javax.validation.constraints.NotNull;
   import javax.validation.constraints.Pattern;
   
   /**
    * 品牌
    * 
    * @author JibalLi
    * @email 905001136@qq.com
    * @date 2021-11-21 22:48:56
    */
   @Data
   @TableName("pms_brand")
   public class BrandEntity implements Serializable {
   	private static final long serialVersionUID = 1L;
   
   	/**
   	 * 品牌id
   	 */
   	@TableId
   	private Long brandId;
   	/**
   	 * 品牌名
   	 */
   	@NotNull
   	private String name;
   	/**
   	 * 品牌logo地址
   	 */
   	@NotNull
   	@URL
   	private String logo;
   	/**
   	 * 介绍
   	 */
   	private String descript;
   	/**
   	 * 显示状态[0-不显示；1-显示]
   	 */
   	private Integer showStatus;
   	/**
   	 * 检索首字母
   	 */
   	@NotNull
   	@Pattern(regexp="[a-zA-z]")
   	private String firstLetter;
   	/**
   	 * 排序
   	 */
   	
   	private Integer sort;
   
   }
   
   
   // 业务层处理校验的逻辑代码处
   
   /**
   * 修改
   * 此处使用了校验功能并进行了一些错误的返回
   */
   @RequestMapping("/update")
   public R update(@Valid @RequestBody CategoryEntity category, BindingResult result){
       if (result.hasErrors()) {
           Map<String,String> map = new HashMap<>();
           result.getFieldErrors().forEach((error)->{
               String filed = error.getField();    // 获取出现错误的字段
               String message = error.getDefaultMessage();   // 获取出错的原因
               map.put(filed,message);
           });
           return R.error(400,"出现错误").put("data",map);    // 将错误的信息返回给请求客户端
       }
       else {
           // 没有错误表示数据可以，可以交给数据库进行更新
           categoryService.updateById(category);
           return R.ok();
       }
   }
   ```

   

### 6.3 异常处理

对于数据校验的功能可以使用一个统一的异常处理,

创建一个统一的异常处理ControllerAdvice建议，在这个类的内部可以定义不同的异常的处理方法

```java
package com.jiabao.jbmall.product.exception;

import com.jiabao.jbmall.common.utils.R;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.HashMap;
import java.util.Map;

/**
 * 集中处理数据异常
 */
@Slf4j
@ResponseBody
@ControllerAdvice
public class ExceptionControllerAdvice {

    // 可以指定该方法需要去处理的异常
    @ExceptionHandler(value = MethodArgumentNotValidException.class)
    public R handlerValidException(Exception e) {
        if (e instanceof MethodArgumentNotValidException) {
            Map<String,String> map = new HashMap<>();
            ((MethodArgumentNotValidException) e).getBindingResult().getFieldErrors().forEach((error)->{
                String filed = error.getField();    // 获取出现错误的字段
                String message = error.getDefaultMessage();   // 获取出错的原因
                map.put(filed,message);
            });
            return R.error(400,"出现错误").put("data",map);    // 将错误的信息返回给请求客户端
        }else {
            return R.error();
        }
    }
}



// 枚举类的使用

package com.jiabao.jbmall.product.exception;

/**
 * 创建一个统一的枚举类来控制错误状态码及其对应的错误信息，后面方便使用统一的获取异常错误信息
 */
public enum ExceptionCodeEnum {
    VALID_EXCEPTION(10001,"参数格式校验失败"),
    UNKOWN_EXCEPTION(10000,"系统位置异常错误");


    // 创建枚举类的状态码和提示消息的属性

    public int getCode() {
        return code;
    }

    public String getMessage() {
        return message;
    }

    private int code;
    private String message;

    ExceptionCodeEnum(int code,String message) {
        this.code = code;
        this.message = message;
    }


}
```

### 6.4 分组校验

为什么需要分组校验：在不同的场景下我们需要校验的字段不同，因此可以在校验的注解出指定groups，这个groups的值是一些接口

**分组校验的步骤：**

1. 在需要进行分组校验的字段位置处指定group属性，这个是一个列表，指定多个需要进行的校验规则和处理步骤的接口类`@NotNull(groups={AddGroup.class})`
2. 创建好需要用来做分组校验的接口即可，可以使用空接口
3. 在controller层的时候需要使用`@Validated({AddGroup.class})`指定什么时候需要进行这一个分组下的校验功能

```java
package com.jiabao.jbmall.product.entity;

import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;

import java.io.Serializable;
import java.util.Date;

import com.jiabao.jbmall.common.valid.AddGroup;
import com.jiabao.jbmall.common.valid.UpdateGroup;
import lombok.Data;
import org.hibernate.validator.constraints.URL;

import javax.validation.constraints.NotNull;
import javax.validation.constraints.Null;
import javax.validation.constraints.Pattern;

/**
 * 品牌
 * 
 * @author JibalLi
 * @email 905001136@qq.com
 * @date 2021-11-21 22:48:56
 */
@Data
@TableName("pms_brand")
public class BrandEntity implements Serializable {
	private static final long serialVersionUID = 1L;

	/**
	 * 品牌id
	 */
    // groups来制定需要进行的校验注解接口类，结合上在controller层的Validated注解中写入的接口类就可以确定这个字段是不是需要进行校验了
	@Null(message = "新增品牌的时候不需要指定ID",groups={AddGroup.class})
	@NotNull(message = "更新品牌的信息的时候需要指定ID",groups={UpdateGroup.class})
	@TableId
	private Long brandId;
	/**
	 * 品牌名
	 */
	@NotNull
	private String name;
	/**
	 * 品牌logo地址
	 */
	@NotNull
	@URL(message = "不管新增还是修改，URL都需要合法",groups = {AddGroup.class,UpdateGroup.class})
	private String logo;
	/**
	 * 介绍
	 */
	private String descript;
	/**
	 * 显示状态[0-不显示；1-显示]
	 */
	private Integer showStatus;
	/**
	 * 检索首字母
	 */
	@NotNull
	@Pattern(regexp="[a-zA-z]")
	private String firstLetter;
	/**
	 * 排序
	 */

	private Integer sort;

}


// Controller层的一些修改内容

package com.jiabao.jbmall.product.controller;

import java.util.Arrays;
import java.util.Map;
import com.jiabao.jbmall.common.utils.PageUtils;
import com.jiabao.jbmall.common.utils.R;
import com.jiabao.jbmall.common.valid.AddGroup;
import com.jiabao.jbmall.common.valid.UpdateGroup;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import com.jiabao.jbmall.product.entity.BrandEntity;
import com.jiabao.jbmall.product.service.BrandService;


/**
 * 品牌
 *
 * @author JibalLi
 * @email 905001136@qq.com
 * @date 2021-11-21 22:48:56
 */
@RestController
@RequestMapping("product/brand")
public class BrandController {
    @Autowired
    private BrandService brandService;

    /**
     * 列表
     */
    @RequestMapping("/list")
    public R list(@RequestParam Map<String, Object> params){
        PageUtils page = brandService.queryPage(params);

        return R.ok().put("page", page);
    }


    /**
     * 信息
     */
    @RequestMapping("/info/{brandId}")
    public R info(@PathVariable("brandId") Long brandId){
		BrandEntity brand = brandService.getById(brandId);

        return R.ok().put("brand", brand);
    }

    /**
     * 保存
     */
    @RequestMapping("/save")
    public R save(@Validated({AddGroup.class}) @RequestBody BrandEntity brand){
		brandService.save(brand);

        return R.ok();
    }

    /**
     * 修改
     */
    // 只需要在这里指定什么分组下需要进行这个数据的校验即可
    @RequestMapping("/update")
    public R update(@Validated({UpdateGroup.class}) @RequestBody BrandEntity brand){
		brandService.updateById(brand);

        return R.ok();
    }

    /**
     * 删除
     */
    @RequestMapping("/delete")
    public R delete(@RequestBody Long[] brandIds){
		brandService.removeByIds(Arrays.asList(brandIds));

        return R.ok();
    }

}

```

### 6.5 自定义注解校验规则

使用注解来进行创建：

1. 编写自定义校验注解，可以参照已有的注解创建规则去进行相应内容的创建书写

   ```java
   //
   // Source code recreated from a .class file by IntelliJ IDEA
   // (powered by FernFlower decompiler)
   //
   
   package javax.validation.constraints;
   
   import java.lang.annotation.Documented;
   import java.lang.annotation.ElementType;
   import java.lang.annotation.Repeatable;
   import java.lang.annotation.Retention;
   import java.lang.annotation.RetentionPolicy;
   import java.lang.annotation.Target;
   import javax.validation.Constraint;
   import javax.validation.Payload;
   
   // 一般就是注解常用的元注解信息
   @Target({ElementType.METHOD, ElementType.FIELD, ElementType.ANNOTATION_TYPE, ElementType.CONSTRUCTOR, ElementType.PARAMETER, ElementType.TYPE_USE})
   @Retention(RetentionPolicy.RUNTIME)
   @Documented
   // 这个位置需要绑定自定义的校验器
   @Constraint(
       validatedBy = {}
   )
   public @interface ListValues {
       String message() default "{javax.validation.constraints.NotNull.message}";
   
       Class<?>[] groups() default {};
   
       Class<? extends Payload>[] payload() default {};
       
       // 自定义的列表值，值的类型只能是列表中的值
       int[] values() default {};
   }
   
   ```

2. 编写一个自定义的校验器

   ```java
   // 自定义校验器
   
   public class ListValuesConstraintValidator implements ConstraintValidator<ListValues,Integer> {
       
       @Override
       public void initialize(ListValues constraintAnnotation) {
           
       }
   }
   ```

   

3. 关联自定义的校验器和校验规则

   ```java
   // 在自定义的校验注解的元注解处指定
   @Constraint(
       validatedBy = {}    // 在这里指定自定义的校验器
   )
   ```

4. 

### 6.6 JsonInclude的使用

是否将这个字段包含到返回值数据集合中去

```java
@JsonInclude(JsonInclude.Include.NON_EMPTY)   // 数据不为空就返回到数据集合中
@JsonInclude(JsonInclude.Include.ALWAYS)     ///不管数据什么情况都返回，默认
@JsonInclude(JsonInclude.Include.USE_DEFAULTS)   // 使用默认值
// 总之如果返回值有限定的化，可以使用这个注解来注解实体类中的某一个字段
```

### 6.7 Dao层和Mapper搭配书写sql语句查询

dao层的代码

```java
package com.jiabao.jbmall.product.dao;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.jiabao.jbmall.common.vo.product.SpuItemAttrGroupVO;
import com.jiabao.jbmall.product.entity.AttrGroupEntity;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;

import java.util.List;


/**

 * 
 * @author JibalLi
 * @email 905001136@qq.com
 * @date 2021-11-21 22:48:56
 */
@Mapper
public interface AttrGroupDao extends BaseMapper<AttrGroupEntity> {
    /**  shfuiahi */
    List<SpuItemAttrGroupVO> getAttrGroupWithAttrsByCatalogId(@Param("catalogId") Long catalogId, @Param("spuId") Long spuId);
}

```



```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.jiabao.jbmall.product.dao.AttrGroupDao">

	<!-- 可根据自己的需求，是否要使用 ,一般只要出现嵌套的属性就需要自定义封装结果-->
    <resultMap type="com.jiabao.jbmall.product.entity.AttrGroupEntity" id="attrGroupMap">
        <result property="attrGroupId" column="attr_group_id"/>
        <result property="attrGroupName" column="attr_group_name"/>
        <result property="sort" column="sort"/>
        <result property="descript" column="descript"/>
        <result property="icon" column="icon"/>
        <result property="catelogId" column="catelog_id"/>
    </resultMap>
    <!-- 自定义的结果映射,里面的id用来标识，resultMap指定结果集映射类型的时候就是用这个id标识 -->
    <resultMap id="spuItemAttrGroupVO" type="com.jiabao.jbmall.common.vo.product.SpuItemAttrGroupVO">
        <result property="groupName" column="groupName"></result>
        <collection property="attrs" ofType="com.jiabao.jbmall.common.vo.product.Attrs">
            <result property="attrName" column="attrName"></result>
            <result property="attrValue" column="attrValue"></result>
        </collection>
    </resultMap>

    <!-- 这个位置，id标识dao层的方法名，resultMap标识为上面定义的结果映射，对于复杂或者嵌套类型，都需要自定义映射 -->
    <select id="getAttrGroupWithAttrsByCatalogId" resultMap="spuItemAttrGroupVO">
        select
            ppav.spu_id spuId,
            pag.catelog_id cateLogId,
            pag.attr_group_name groupName,
            pa.attr_name attrName,
            ppav.attr_value attrValue
        from `pms_attr_group` pag
                 left join `pms_attr_attrgroup_relation` paagr on pag.attr_group_id=paagr.attr_group_id
                 left join `pms_attr` pa on paagr.attr_id=pa.attr_id
                 left join `pms_product_attr_value` ppav on ppav.attr_id=pa.attr_id
        where pag.`catelog_id`=#{catalogId} and ppav.`spu_id`=#{spuId}
    </select>

</mapper>
```

### 6.8 异步任务优化业务

创建一个线程池工厂，一般可以写上一个配置类来创建一个线程池对象，此外还可以对这个线程池的一些参数可进行配置，可以使用上properties文件来作为配置文件加载配置项

```java
package com.jiabao.jbmall.product.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.concurrent.Executors;
import java.util.concurrent.LinkedBlockingQueue;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

/**
 * @Author: JiabaoLi
 * @Email: 905001136@qq.com
 * @CreatedDate: 2021/12/13
 */

// 这个是创建用来注入ThreadPoolExecutor的配置类，此外还和引入了配置类来实现可配置化的自动注入
@Configuration
public class MyThreadPoolConfig {

    @Bean
    public ThreadPoolExecutor getThreadPoolExecutor(MyThreadPoolConfigProperties threadPoolConfigProperties) {

        return new ThreadPoolExecutor(threadPoolConfigProperties.getCoreSize(),
                threadPoolConfigProperties.getMaxThreadSize(),threadPoolConfigProperties.getKeepAliveTime(),
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(threadPoolConfigProperties.getWorkQueueSize()),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());
    }
}
```

配置文件管理类：加入了component可以自动注入

[spring官方的资源配置类使用教程](https://docs.spring.io/spring-boot/docs/2.3.7.RELEASE/reference/html/appendix-configuration-metadata.html#configuration-metadata-annotation-processor)

```java
package com.jiabao.jbmall.product.config;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

/**
 * @Author: JiabaoLi
 * @Email: 905001136@qq.com
 * @CreatedDate: 2021/12/13
 */
// 指定配置文件的配置内容的前缀
@ConfigurationProperties(prefix = "jbmall.thread")
@Component
@Data
public class MyThreadPoolConfigProperties {

    private Integer coreSize=50;
    private Integer maxThreadSize=200;
    private Integer keepAliveTime=30;
    private Integer workQueueSize=10000;
}
```

需要加入配置类处理器的依赖到项目的依赖中：

```xml
<!-- 配置类处理器，用来处理配置文件的内容 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```



异步任务优化代码：

- 原来的业务执行逻辑是串行的执行，这样的话，执行后面的任务的时候，就需要先等待前面的任务完成才可以，但是实际上，很多的业务之间相互不影响的话，可以使用异步来优化，可以大大的减少业务的耗时
- 此外异步任务的时候注意，返回结果和所有的任务挂钩的时候，一定要等待所有的任务完成才可以，可以使用allOf来阻塞等待，就是将所有的future加入然后使用get这一阻塞方法获取结果让任务在调用位置阻塞等待

```java
// 使用异步任务优化的代码，但是在使用异步任务的时候
//一定注意，如果方法的返回值是所有的任务都需要执行完成才可以获取到的话，那么一定要等待所有的异步完成之后才能够返回
// 如果不等待返回，得到的结果就不是你想要的结果
public SkuItemVO getAllSkuItem(Long skuId) {
    // TODO 在这里由于查询的数据比较多，且数据和数据之间没有什么直接关系，可以使用异步的方式加快查询速度
    SkuItemVO skuItem = new SkuItemVO();
    // 1 查询SkuInfo
    CompletableFuture<SkuInfoEntity> infoFuture = CompletableFuture.supplyAsync(() -> {
        SkuInfoEntity info = getById(skuId);
        skuItem.setInfo(info);
        return info;
    }, executor);
    // 2 查询SkuImages
    CompletableFuture<Void> imageFuture = CompletableFuture.runAsync(() -> {
        List<SkuImagesEntity> images = skuImagesService.getImagesBySkuId(skuId);
        skuItem.setImages(images);
    }, executor);
    // 3 查询spu组合销售属性
    CompletableFuture<Void> saleAttrFuture = infoFuture.thenAcceptAsync(res -> {
        if (res == null) {
            log.debug("没有查询到相关的skuInfo");
        } else {
            List<SkuItemSaleAttrVO> saleAttrVOS = saleAttrValueService.getAllSaleAttrsBySpuId(res.getSpuId());
            skuItem.setSaleAttr(saleAttrVOS);
        }
    }, executor);
    // 4 查询spu的介绍
    CompletableFuture<Void> descFuture = infoFuture.thenAcceptAsync(res -> {
        if (res != null) {
            SpuInfoDescEntity spuInfoDesc = spuInfoDescService.getById(res.getSpuId());
            skuItem.setDesc(spuInfoDesc);
        } else {
            log.debug("没有查询到相关的skuInfo");
        }
    }, executor);
    // 5 查询spu的规格参数属性
    CompletableFuture<Void> itemFuture = infoFuture.thenAcceptAsync((res) -> {
        if (res == null) {
            log.debug("没有查询到相关的skuInfo");
        }
        else {
            List<SpuItemAttrGroupVO> itemAttrGroupVOS = attrGroupService.getAttrGroupWithAttrsByCatalogId(res.getCatalogId(), res.getSpuId());
            skuItem.setGroupAttrs(itemAttrGroupVOS);
        }
    }, executor);

    // 将上面的异步任务创建完成之后，需要等待上面的所有任务完成才能够返回
    try {
        CompletableFuture.allOf(infoFuture,itemFuture,saleAttrFuture,imageFuture,descFuture).get();
    } catch (InterruptedException e) {
        log.error("发生了中断异常"+e.getMessage());
    } catch (ExecutionException e) {
        log.error("发生了执行异常"+e.getMessage());
    }
    return skuItem;
}
```

### 6.9 短信验证码业务

首先需要调用第三方服务，这个第三方服务是专门用来保存书写那些需要调用第三方公司提供的一些业务来实现的

- 比如oss云存储服务
- 比如sms短信验证码服务

验证码服务有很多的细节需要注意：

- 保证一个手机号在多少时间内只能发送一次验证码请求，这个需要使用到redis来作缓存，缓存的键值可以自定义，只要后续的解析按照自己的定义的模式来进行即可。redis将发送的验证码的和当前系统时间做一个共同保存，当某个手机号进来请求验证码的时候需要首先验证是否已经有验证码存在，并判断时间是否已经过去了指定的时间
- 接口防刷：防止你的短信验证码接口暴露给别人，然后别人通过这个暴露接口来不断发起短信验证码请求，这个可以对验证码接口做一个隐藏或者后台做一个验证管理，防止被恶意请求

<font color='red'>post->get：转发post到get是不支持的，因此在thymeleaf使用转发请求的时候需要注意一下这个问题</font>

转发：`forward:/`

重定向：`redirect:/`



### 6.10 异常机制

有的时候业务层和controller层为了方便，可以采用异常机制来进行一些通信，两者之间做一个协议规定即可知道一些基础的异常原因，然后controller就可以根据这些异常的信息来处理



### 6.11 加密实现

有一个加密工具类：`org.apache.common.codec.digest.DigestUtils`，这个工具类里面有很多的加密算法的实现

加密算法的几种类别：

- 可逆
- 不可逆
- 对称
- 非对称

密码加密的实现：使用MD5加密。MD5破解实现是通过计算处不同密码的MD5存储到数据结构中，之后传入的MD5值去查找对应的原密码。--彩虹表

MD5盐值加密，可以使用spring的加密器

```java

```



### 6.12 注册业务实现

- 前端注册数据的校验+后端数据的校验，使用JSR303校验进行，可以参考之前的一些校验方案去写
- 短信验证码的发送验证
- 后台验证用户名和手机号是否已经注册过了，如果注册过了，可以在业务层里面返回异常，然后让controller去感知异常然后进行处理并返回给前端页面给出提示。

### 6.13 认证服务

第三方认证，OAuth2.0。

申请第三方账号认证的流程：

1. 应用程序向用户展示第三方认证的选择页，向用户确认想要向哪一个第三方发起请求
2. 应用程序向用户指定的第三方发起认证请求，然后返回登录信息确认的界面给到用户
3. 用户自己确定并填写相关的账号密码信息，提交登录
4. 第三方认证账号密码是否正确
5. 正确就向应用程序返回一个token信息，这个信息只能使用一次来换取access token
6. 应用程序拿到这个信息再向第三方发起请求获取访问令牌
7. 应用程序拿到这个令牌之后就可以向第三方获取一些公用的显示的信息
8. 然后应用程序根据这些信息主动的位用户创建账号信息并登录
9. 返回登录成功的界面

微博的第三方认证登录权限申请：

- 进入微博开放平台首页申请，登录之后可以选择网络接入去申请
- 申请的是应用开发的创建界面，按照流程申请 

码云请求认证code的方法（get请求）：https://gitee.com/oauth/authorize?client_id=2fec6869b3af326699f2c0491e9d3d8dd70cbdccd230c131ed53587862d951e8&redirect_uri=http://jbmall.com&response_type=code

微博请求认证code的方法（get请求）：

码云请求access_token的方法（post请求）：https://gitee.com/oauth/token?grant_type=authorization_code&code={code}&client_id={client_id}&redirect_uri={redirect_uri}&client_secret={client_secret}

在这里首先获取到code之后，然后后端利用code信息发送一个post请求来获取accessToken。这个操作可以借助HttpClient（apache公司的工具包）发起http的请求，并解析响应的内容。然后再去发送一个后端的查询用户数据的请求来获取到基本的用户信息，然后查询数据库判断是否之前已经登录过了。





### 6.14 session

#### session共享问题

- 分布式集群下，不同机器上的相同服务不能共享session的问题
- 服务和服务之间session不共享的问题

#### 分布式session的解决

1. 使用session复制同步方案，
   优点：

   - 原生的tomcat支持session复制，只需要修改配置文件

   缺点：

   - 问题在于session的复制和同步需要不同的tomcat服务器之间相互传输数据
   - 每台服务器都需要保存session的数据，消耗大量的内存空间
   - 内存限制，不能扩展太多的服务器（水平扩展）因此在大的分布式集群中，这个方案不可取

2. 客户端自己存储session数据：
   优点：

   - 用户保存自己的session信息到cookie中，节省服务端资源

   缺点：

   - 每次请求带上cookie，数据传输很慢，浪费网络带宽
   - 此外cookie有长度限制，并不能保存很多的信息，
   - session放入cookie，cookie可以看到修改的，可能会暴露信息，存在安全隐患

3. 哈希一致性：让每一次客户端的访问每一次都落到相同的服务器上
   优点：

   - 利用nginx的负载均衡算法，来保证每一次的负载均衡都会落到相同的服务器
   - 这就涉及到分布式下的哈希一致性算法，算法的可靠性很重要
   - 支持服务器的水平扩展，可以扩展很多的服务器

   缺点：

   - 服务器水平扩展后，rehash可能导致部分用户hash到新的服务器上，session资源丢失
   - 服务器存储session，服务器挂掉session丢失

   尽管存在问题，但是因为session有期限，可以使用这个方案

4. 统一存储：采用redis存储用户的session信息
   优点：

   - 没有安全隐患，数据安全的存储在中间件中，每次访问都由服务器去中间件取用
   - 服务器扩容并不影响session存储在其他的中间件服务器上

   缺点：

   - 中间件的可靠性问题
   - 增加了一次网络调用的问题，需要修改业务代码查询redis的session。从redis读取数据比直接从服务器内存读取数据更慢

   不过spring有一个SpringSession可以解决问题

5. 使用SpringSession

#### session跨域、子域和跨服务问题

子域共享问题：在保存cookie信息的时候，设置域名，设置域名为最大的一层域名，即使是父域名，也可以访问到cookie信息

```java
HttpSession.setAttribute();    // 设置session信息到cookie中
new Cookie().setDomain();         // cookie设定域名即可，不设置就是默认当前页面的域名
HttpServletResponse.addCookie();  // 然后再添加cookie
```



### 6.15 SpringSession整合

1. 加入SpringSession依赖：

```xml
<dependencies>
	<!-- ... -->

	<dependency>
		<groupId>org.springframework.session</groupId>
		<artifactId>spring-session-data-redis</artifactId>
	</dependency>
</dependencies>
```

2. 配置session存储类别：配置文件中加入`spring.session.store-type=redis # Session store type`

3. 配置redis的连接信息

   ```properties
   spring.session.store-type=redis # Session store type.
   
   spring.redis.host=localhost # Redis server host.
   spring.redis.password= # Login password of the redis server.
   spring.redis.port=6379 # Redis server port.
   ```

4. servlet容器初始化：

5. 开启SpringSession：`@EnableRedisHttpSession`注解用在Application类上

自定义SpringSession配置：[SpringSession相关的API](https://docs.spring.io/spring-session/reference/api.html)

```java
/*
 * Copyright 2014-2019 the original author or authors.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      https://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

package sample.config;

import com.fasterxml.jackson.databind.ObjectMapper;

import org.springframework.beans.factory.BeanClassLoaderAware;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.security.jackson2.SecurityJackson2Modules;

/**
 * @author jitendra on 3/3/16.
 */
@Configuration
public class SessionConfig implements BeanClassLoaderAware {

	private ClassLoader loader;

    // 配置session序列化器，不使用默认的JDK序列化工具
	@Bean
	public RedisSerializer<Object> springSessionDefaultRedisSerializer() {
		return new GenericJackson2JsonRedisSerializer(objectMapper());
	}

    // 配置跨作用域的问题
	@Bean
	public CookieSerializer cookieSerializer() {
		// 需要新建一个默认的cookie构建器
        DefaultCookieSerializer serializer = new DefaultCookieSerializer();
        // 设置cookie名字
		serializer.setCookieName("JSESSIONID"); 
        // 设置cookie的作用路径
		serializer.setCookiePath("/"); 
        serializer.setDomainName("jbmall.com");
        // 设置作用域的正则表达式
		serializer.setDomainNamePattern("^.+?\\.(\\w+\\.[a-z]+)$"); 
		return serializer;
	}
    
    
	/**
	 * Customized {@link ObjectMapper} to add mix-in for class that doesn't have default
	 * constructors
	 * @return the {@link ObjectMapper} to use
	 */
	private ObjectMapper objectMapper() {
		ObjectMapper mapper = new ObjectMapper();
		mapper.registerModules(SecurityJackson2Modules.getModules(this.loader));
		return mapper;
	}

	/*
	 * @see
	 * org.springframework.beans.factory.BeanClassLoaderAware#setBeanClassLoader(java.lang
	 * .ClassLoader)
	 */
	@Override
	public void setBeanClassLoader(ClassLoader classLoader) {
		this.loader = classLoader;
	}

}
```

#### <font color='red'>整合springsession的错误</font>

注意session一旦使用了SpringSession，其他的服务想要获取这一个session数据的话，都需要开启springsession并配置好相同的springsession的配置，不然还是会出现错误的



#### SpringSession原理

`EnableRedisHttpSession`注解中Import了`RedisHttpSessionConfiguration`配置类

`SessionRepository`存储session的地方

`RedisOperationsSessionRepository`：redis操作session的封装类，封装了session的增删改产

`SessionRepositoryFilter`：session存储过滤器，每个请求经过都会经过Filter



核心内容：

`SessionRepositoryFilter`中的`doFilterInternal()`方法，对于session数据在redis里面也是做了一个自动续期的操作的

里面的两个包装类重写了getSession()方法。

```java
// 后面的原理就是包装，装饰器模式

protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
    // 设置请求的参数，当前的请求共享同一个sessionRepositry，保证了数据的共享
    request.setAttribute(SESSION_REPOSITORY_ATTR, this.sessionRepository);
    // 将请求和响应封装到一个SessionRepositoryWrapper里面
    SessionRepositoryFilter<S>.SessionRepositoryRequestWrapper wrappedRequest = new SessionRepositoryFilter.SessionRepositoryRequestWrapper(request, response);
    //响应封装
    SessionRepositoryFilter.SessionRepositoryResponseWrapper wrappedResponse = new SessionRepositoryFilter.SessionRepositoryResponseWrapper(wrappedRequest, response);

    try {
        // 放行包装之后的请求和响应
        filterChain.doFilter(wrappedRequest, wrappedResponse);
    } finally {
        wrappedRequest.commitSession();
    }

}
```

### 6.16 单点登录

#### 多系统跨域session共享问题

不同系统的同一个公司账号的通用。

单点登录，一处登录，处处登录



#### xxl单点登录框架

实现：

- 设置中央认证服务器，作为所有登录请求的处理服务器
- 其他系统想要登录，让所有的登录请求转到中央认证服务器
- 只要一个登录成功，其他的也都登录成功
- 全系统（不同域名）统一使用一个session

单点登录的完整流程：

1. 访问受保护的资源需要登录的时候，就跳转到登录页面申请认证
2. 认证请求发送到中央认证服务器来进行统一的认证
2. 请求发送到认证服务器之后用户进行登录操作
2. 登录成功之后会设置cookie信息，设置的cookie信息会带上用户登录之后的一些重要信息
2. 返回登录之前所处的页面，直接获取到用户登录成功之后的信息（可以再这里面再去进行查询用户信息的请求）
2. 还有其他不同域名但是用户信息相同的登录请求时，也会继续跳转到中央认证服务器进行认证，但是这个时候由于之前已经有登录成功之后设置的cookie信息，再次发起认证请求的时候会带上已有的cookie信息，然后服务器进行判断是否已经有用户登录，有就直接跳转回原来的登录请求之前的页面并获取用户信息
2. 没有就进行登录操作，登录成功之后设置cookie到当前域名的cookie信息

### 6.17 拦截器

在执行方法的时候进行拦截，然后去处理想要的操作之后再去执行方法。这是一种业务的加强操作，是一种设计模式或者一种设计理念，可以很好的避免修改过多的代码，只需要在某些位置进行修改即可增强功能。

当session信息判断某个用户是否登录的操作，这种操作就可以使用前置拦截器来进行预先的判定，通过返回值判断。

拦截器由于拦截之后可能还会操作获取到某些信息数据，需要和另外的位置进行数据共享，可以使用ThreadLocal进行数据的共享。ThreadLocal是线程内部进行数据共享的。

拦截器的方法：

- **preHandle()方法**：该方法在执行控制器方法之前执行。返回值为Boolean类型，如果返回false，表示拦截请求，不再向下执行，如果返回true，表示放行，程序继续向下执行（如果后面没有其他Interceptor，就会执行controller方法）。所以此方法可对请求进行判断，决定程序是否继续执行，或者进行一些初始化操作及对请求进行预处理。
- **postHandle()方法**：该方法在执行控制器方法调用之后，且在返回ModelAndView之前执行。由于该方法会在DispatcherServlet进行返回视图渲染之前被调用，所以此方法多被用于处理返回的视图，可通过此方法对请求域中的模型和视图做进一步的修改。
- **afterCompletion()方法**：该方法在执行完控制器之后执行，由于是在Controller方法执行完毕后执行该方法，所以该方法适合进行一些资源清理，记录日志信息等处理操作。比如ThreadLocal的清理也是可以的

拦截器的使用流程：

- 创建自定义的拦截器，根据自己的需求写上拦截器处理的内容。可以创建多个
- 创建配置类，这个配置类需要实现WebMvcConfigurer接口，并重写addInterceptor方法
- 在重写的方法体内部添加拦截器和拦截的url，可以指定多个拦截器拦截你想要拦截的url就可以了

```java
// 创建拦截器，创建好之后需要创建一个配合类设置添加拦截器，针对你需要拦截的那些请求就可以了
public class CartInterceptor implements HandlerInterceptor {
    // 前置拦截器，在执行之前处理的
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        return HandlerInterceptor.super.preHandle(request, response, handler);
    }
    
    // 后置处理器，在拦截方法执行之后处理的
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    // 完成方法之后的处理的
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // 执行线程内部共享变量的清理工作，防止忘记清理导致的OOM
        threadLocal.remove();
    }
}
```

```java
package com.jiabao.jbmall.cart.config;

import com.jiabao.jbmall.cart.interceptor.CartInterceptor;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * 实现mvc的配置类，添加拦截器和默认的视图控制器
 * @Author: JiabaoLi
 * @Email: 905001136@qq.com
 * @CreatedDate: 2021/12/19
 */
@Configuration
public class MyWebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        // 添加url和view的映射，当访问某url的时候直接跳转到指定的view视图渲染内容
        registry.addViewController("/cart.html").setViewName("cartList");
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 重写添加拦截器的方法并制定拦截器以及拦截器需要拦截的url
        registry.addInterceptor(new CartInterceptor()).addPathPatterns("/**");
    }
}

```

### 6.18 ThreadLocal

ThreadLocal使用场景：

- 在一个线程中，跨越了多个方法的调用，需要传递某些数据或者对象（也可称之为上下文），是一种状态，可以是用户身份、用户信息等
- 这种场景增加context参数非常麻烦，此外还有些方法不能修改参数或者修改源码，这些信息就传入不进来
- 这种时候就可以使用ThreadLocal，可以在同一个线程中传递数据（对象）

ThreadLocal使用示例：

```java
public class ThreadLocalTest {
    public static ThreadLocal<User> threadLocal = new ThreadLocal<>();
    
    // thread使用的主体方法
    public void processUser(User user) {
        try {
            // 首先设置线程共享的变量信息
            threadLocal.set(user);
            // 然后在当前线程的其他方法体之中都可以使用这些信息
            log();
            step1();
            step2();
        }finally{
            // 使用finally方法体来保证不管方法是否执行成功，都会去进行ThreadLocal的清理操作
            // ThreadLocal使用完毕一定要记得及时清除里面存储的数据，不然可能导致后面出现bug
            // 比如当前线程用户访问之前保留的用户信息，或者由于内存堆积没有被清理，导致OOM异常
            threadLocal.remove();
        }
    }
    void step1() {
        User u = threadLocalUser.get();
        log();
        printUser();
    }

    void log() {
        User u = threadLocalUser.get();
        println(u.name);
    }

    void step2() {
        User u = threadLocalUser.get();
        checkUser(u.id);
    }
}
```

ThreadLocal在使用的过程中的一些问题：

- 当前线程执行完相关代码后，很可能会被重新放入线程池中，如果`ThreadLocal`没有被清除，该线程执行其他代码时，会把上一次的状态带进去。

为了保证释放ThreadLocal关联的实例，可以通过AutoCloseable接口配合try(resource)语句结构，让编译器为我们自动关闭：

```java
// 实现AutoCloseable接口，来使用try(resource)结构体保证一定会清理掉某个对象内容
public class UserContext implements AutoCloseable {

    static final ThreadLocal<String> ctx = new ThreadLocal<>();

    public UserContext(String user) {
        ctx.set(user);
    }

    public static String currentUser() {
        return ctx.get();
    }

    @Override
    public void close() {
        ctx.remove();
    }
}


// 使用的场景
try (var ctx = new UserContext("Bob")) {
    // 可任意调用UserContext.currentUser():
    String currentUser = UserContext.currentUser();
} // 在此自动调用UserContext.close()方法释放ThreadLocal关联对象
```



### 6.1 幂等性



### 6.1  购物车业务实现





#### 防止接口重复调用导致bug出现

实现方法就是将api接口调用成功之后直接切换到另外一个url地址展示添加结果，这样就可以防止某个接口被无限次调用导致bug。利用重定向功能实现，此外如果重定向的页面需要带上model数据的话，不能使用model来进行属性的添加，因为重定向model数据不能跨页面共享，需要使用`RedirectAttributes`

这个类下面的一些方法：

- `addFlashAttribute()`：将数据放在session里面，可以在页面取出这些数据，但是只能取用一次
- `addAttribute()`：将数据放在重定向的url中拼接



## 7 商品的SPU和SKU

具体的很多的业务逻辑暂时就先跳过了，先学习后面的高级篇章，但其实这些东西在实际中有用，但是对于面试，可能不会很多的询问你基本的业务逻辑上的一些东西，因此，后续再去看这些东西或者等到工作再去干这些

## 8 商城的搜索系统和服务

基于elasticsearch来进行搜索展示，elasticsearch的官方工具类库Elasticsearch-Rest-Client是官方的推荐连接客户端

通过这个客户端向ES发送请求请求想要的数据

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.14.2</version>
</dependency>
```

具体的client的使用可以参照官方文档：

步骤：

1. 首先构建一个RestHighLevelClient客户端连接到ES服务器，连接到服务器之后返回这个客户端
2. 构建RequestOptions，后面进行相应的操作的时候需要传入这个参数，可以先构建一个提供给普遍使用的OPTIONS，后续可以在这个options里面新增一些东西
3. 构建你需要执行的请求操作，比如GetRequest、SearchRequest、PostDataRequest等等，然后使用相应的builder来进行条件的构建以及数据的传入等等
4. 然后将请求和builder进行绑定`request.source(builder)`
5. 然后使用client的search、delete、update、bulk等等的操作方法，需要传入的参数是请求和请求的Options
6. 上面的操作完成之后会返回一个相应对象Response，利用这个response去进行后面的数据处理或者报错等等操作

每次操作的基本流程：

1. 创建请求对象
2. 创建请求构建体对象
3. 客户端发起请求返回响应对象
4. 响应对象处理数据

将数据添加到ES中，使用bulk批量插入数据：

```java
    private BulkRequest buildBulkRequest(List<SkuEsModel> products) {
        BulkRequest request = new BulkRequest();
        for (SkuEsModel product:products) {
            IndexRequest indexRequest = new IndexRequest(EsConstant.PRODUCT_INDEX);
            // 构建插入数据的请求对象并加入bulkrequest中
            indexRequest.source(JSON.toJSONString(product), XContentType.JSON);
            request.add(indexRequest);
        }
        return request;
    }

    private boolean saveProducts(List<SkuEsModel> products) throws IOException {
        // 先构建添加数据到Elasticsearch的请求对象
        BulkRequest request = buildBulkRequest(products);

        // 发起批量操作的请求
        BulkResponse bulk = client.bulk(request, ElasticSearchConfig.COMMON_OPTIONS);
        if (bulk.hasFailures()) {
            log.error("商品保存到ES出错了：{}",bulk.status());
            return false;
        }else {
            List<String> collect = Arrays.asList(bulk.getItems()).stream().map(item -> item.getId()).collect(Collectors.toList());
            log.info("商品保存到ES成功:\n{}",collect);
            return true;
        }
    }
```



### 8.1 搜索业务逻辑

通过ES先定义好查询的逻辑，然后在根据这个搜索逻辑去业务层里面去结合RestHighLevelClient实现具体的搜索业务

ES的查询逻辑：

- 模糊匹配：query下使用bool查询，查询和过滤的条件都在query下面去写
- 过滤条件的使用，bool属性下有一个filter来专门进行过滤
- 排序条件：使用sort进行排序条件的设置
- 分页条件：使用from和size来实现分页的这一功能
- 高亮：highlight，高亮你需要强调显示的内容，可以按照字段指定
- 聚合分析：aggs下面指定每一个聚合分析的名字和具体的聚合操作。聚合下面还可以使用子聚合来获取上一次聚合之后的其他结果。聚合主要是为了将查询出来的内容进行一个聚合处理，分别聚合品牌id以及品牌名、聚合商品的所有属性

需要注意：如果创建对应的映射的时候使用了nested（按照对象来进行处理，不会进行扁平化处理），那么在后面的查询、排序、聚合等操作的时候，都需要指定nested的字段的处理方法，就是以nested开头先指定path，然后在后面再去进行这一字段的查询、排序和聚合的。商品的attrs就指定为了nested嵌入式的

```json
// 这是一个比较完整的搜索条件的实际查询语句
// 涉及到了查询、排序、分页、高亮、聚合等操作的具体查询使用

//GET gulimall_product/_search
{
  "query": {
    "bool": {
      "must": [
        {
          // 模糊查询条件，查询标题中包含指定内容的数据
          "match": {
            "skuTitle": "华为"
          }
        }
      ],
      // 过滤条件的设定
      "filter": [
        {
          "term": {
            "catalogId": "225"
          }
        },
        {
          "terms": {
            "brandId": [
              "2"
            ]
          }
        },
        {
          "term": {
            "hasStock": "false"
          }
        },
        {
          "range": {
            "skuPrice": {
              "gte": 1000,
              "lte": 7000
            }
          }
        },
          // 注意设置为内嵌式的字段的查询
        {
          "nested": {
            "path": "attrs",
            "query": {
              "bool": {
                "must": [
                  {
                    "term": {
                      "attrs.attrId": {
                        "value": "6"
                      }
                    }
                  },
                  {
                    "terms": {
                      "attrs.attrValue": [
                        "2019",
                        "2020"
                      ]
                    }
                  }
                ]
              }
            }
          }
        }
      ]
    }
  },
    // 排序条件的设置
  "sort": [
    {
      "skuPrice": {
        "order": "desc"
      }
    }
  ],
    // 分页
  "from": 0,
  "size": 5,
    // 内容高亮逻辑，可以自己指定
  "highlight": {
    "fields": {"skuTitle": {}},
    "pre_tags": "<b style='color:red'>",
    "post_tags": "</b>"
  },
    // 聚合逻辑的书写
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brandId",
        "size": 10
      },
        // 子聚合，用来查询聚合条件的具体
      "aggs": {
        "brandNameAgg": {
          "terms": {
            "field": "brandName",
            "size": 10
          }
        },

        "brandImgAgg": {
          "terms": {
            "field": "brandImg",
            "size": 10
          }
        }

      }
    },
    "catalogAgg":{
      "terms": {
        "field": "catalogId",
        "size": 10
      },
      "aggs": {
        "catalogNameAgg": {
          "terms": {
            "field": "catalogName",
            "size": 10
          }
        }
      }
    },
      // 内嵌式数据的聚合查询，需要指定nested
    "attrs":{
      "nested": {
        "path": "attrs"
      },
      "aggs": {
        "attrIdAgg": {
          "terms": {
            "field": "attrs.attrId",
            "size": 10
          },
          "aggs": {
            "attrNameAgg": {
              "terms": {
                "field": "attrs.attrName",
                "size": 10
              }
            },
            "attrValueAgg": {
              "terms": {
                "field": "attrs.attrValue",
                "size": 10
              }
            }
          }
        }
      }
    }
  }
}
```



## 9 ELK

指的是ES+kibana+logstash搭建的日志搜索分析系统



## 10 nginx+gateway

nginx做负载均衡，将发送到服务器的请求先全部转给网关，通过网关来做转发或者从服务注册中心查找到用户请求的服务并返回结果

gateway的功能：路由、过滤器，可以用来作为流量控制的作用。网关一般用来做全局流量监控、日志记录、全局限流、黑白名单控制、接入请求到业务系统的负载均衡等。系统和外界联通的入口。

nginx：总的流量入口，反向代理和负载均衡，web服务器

本次业务的架构采取使用了nginx作为外部的负载均衡服务器，实现和前端页面和外部环境的交互通信

网关负责的是和nginx的通信，接受来自nginx的服务请求，然后通过网关进行一些业务处理，过滤等，传递给内部服务器（这里起到的是路由的作用，将实际请求传递给实际消费的服务器的服务上面），一般内部服务会通过注册中心和配置中心进行统一的服务管理，借由服务的注册中心可以指定将指定的请求api传递给指定的服务上面

设置nginx的两个配置，一个是http块下的配置，一个是server块下的配置

```
 http {
 	 // 上游服务器可以设置多个并指定负载均衡的策略，来保证负载均衡，不让某个服务器的压力过大
     upstream jbmall {
        // 将服务请求都转发给网关
     	server 192.168.50.1:88;
     }
 }
```

```
server {
    listen       80;
    listen  [::]:80;
    server_name  jbmall.com;

    #access_log  /var/log/nginx/host.access.log  main;

    location / {
    	// 需要设置请求头尾当前的host保留，不然nginx自动删除了host请求头可能访问不到
        proxy_set_header   Host  $host;
        // 将这个转发到http块下设置的upstream设定的那个名称
        proxy_pass  http://jbmall
    }
}
```

指定域名访问预测规则，将匹配host指定的pattern的访问请求路由到指定的网址：

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: host_route
        uri: lb://jbmall-product
        predicates:
        # 将满足jbmall.com或jbmall.org的域名的请求都转向指定的服务,也就是商品服务
        - Host=**.jbmall.com,**.jbmall.org
```

Nginx的动静分离：

```

```



## 11 压力测试和性能监控

### 11.1 性能监控

#### 11.1.1 jconsole和jvisualvm

（11找不到）

命令行启动，可以用来监控本地和远程应用

这两个工具的主要用途：

- 监控内存泄漏
- 跟踪垃圾回收
- 执行时内存、CPU、线程分析

线程的几种状态：这个工具可以看到

- 运行：正在执行任务的线程
- 休眠：sleep的线程
- 等待：wait的线程
- 驻留：线程池里面的空闲线程
- 监视：阻塞的线程，正在等待锁

#### 11.1.2 jstack

查看jvm的线程运行状态，查看是否有死锁现象等等，查看java进程的堆栈信息

#### 11.1.3 jinfo

可以输出并修改运行时Java进程的opts

#### 11.1.4 jps

用来显示本地的Java进程，查看运行的Java进程和进程号

#### 11.1.5 jstat

vm内存工具，可以用来监视vm的各种堆和非堆的大小及其内存的使用量

#### 11.1.6 jmap

打印某个Java进程内存的所有对象的情况（产生了哪些对象，对象的数量有多少

### 11.2 压力测试

用来测试考察当前硬件环境下系统所能承受的最大负荷并且帮助找到系统的瓶颈所在，压力测试是为了保证系统在线上的处理能力和稳定性维持在一个标注的范围内，做到心中有数

压测主要用来发现并测试的错误和瓶颈：

- 内存泄漏
- 高并发和同步的问题

#### 11.2.1 性能指标

- 响应时间：指从客户端发送一个请求开始到客户短接受到从服务端响应的这一段时间
- HPS（hit per second）：每秒的点击数
- TPS（transaction per second）：每秒处理的交易（事务）数
- QPS（query per second）：每秒的处理查询的次数
- 最大响应时间：用户发出请求到系统作出反应花费的最大时间
- 最少响应时间：用户发出请求到系统作出反应花费的最少时间
- 90%响应时间：响应时间排序，在90%位置的响应时间
- 吞吐量：每秒钟系统所能处理的请求数和任务数
- 响应时间：服务器处理一个请求或一个任务花费的时间
- 错误率：一批请求中结果出错的请求占据的比例

行业和指标的经验值设计：

- 金融行业：1000~50,000TPS，不包含互联网化活动
- 保险行业：100~100,000TPS，不包含互联网化活动
- 制造：10~5000TPS
- 互联网电子商务：10,000~1000,000TPS
- 中型网站：1000~50,000TPS
- 小型网站：500~10,000TPS

#### 11.2.2 压测软件Jmeter使用

步骤：

1. 启动多个线程，添加线程组
   ![image-20211203232007085](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E5%8E%8B%E5%8A%9B%E6%B5%8B%E8%AF%95%E7%BA%BF%E7%A8%8B%E7%BB%84%E5%8F%82%E6%95%B0.png)
2. 添加http请求
3. 添加监视器（监视器可以设置很多种结果分析的选项，可以看到常用的一些性能指标）
4. 启动压力测试
5. 查看分析结果

结果分析：

- 有错误率同开发确认，确定是否允许错误的发生或者错误率允许在多大的范围内；
-  Throughput 吞吐量每秒请求的数大于并发数，则可以慢慢的往上面增加；若在压测的机
  器性能很好的情况下，出现吞吐量小于并发数，说明并发数不能再增加了，可以慢慢的
  往下减，找到最佳的并发数；
-  压测结束，登陆相应的web 服务器查看CPU 等性能指标，进行数据的分析;
-  最大的tps，不断的增加并发数，加到tps 达到一定值开始出现下降，那么那个值就是
  最大的tps。
-  最大的并发数：最大的并发数和最大的tps 是不同的概率，一般不断增加并发数，达到
  一个值后，服务器出现请求超时，则可认为该值为最大的并发数。
-  压测过程出现性能瓶颈，若压力机任务管理器查看到的cpu、网络和cpu 都正常，未达到90%以上，则可以说明服务器有问题，压力机没有问题。
-  影响性能考虑点包括：
  数据库、应用程序、中间件（tomact、Nginx）、网络和操作系统等方面。首先考虑自己的应用属于CPU 密集型还是IO 密集型

### 11.2.3 压测和性能分析

进行压测的过程中，还可以开启jvisualvm或者jconsole工具查看对应的服务的内存、CPU、IO的一些情况，可以通过这些指标并结合压力测试得到的一下性能指标来分析性能瓶颈是哪一部分造成

常见的性能瓶颈：

- 各级中间件：中间件（nginx+网关+tomcat）数量增多性能也是会下降的，后端服务都是tomcat来提供，nginx主要是负责和用户交互，负载均衡，网关是用来过滤流量控制等功能。一般压力测试的时候会分别测试在单独服务、网关+服务、nginx+服务、nginx+网关+服务等情况下的性能指标。中间本身也是可以进行一部分的调优和优化的，比如nginx，可以采用动静分离的方式来减少后端服务的压力，让静态资源的访问交给nginx，后端服务只是负责和数据库等的交互CPU计算方面的内容
- 数据库：数据库的查询时间，这部分的优化就涉及到了MySQL的优化
- 网络IO：各级服务之间的交流通信、服务和网关和nginx之间的交流通信，这部分可能就需要使用到netty这样的高性能网络框架改善网络IO的性能
- 本身服务业务逻辑层的优化：本身代码设计方面的问题、算法实现等
- 服务器的性能瓶颈：服务器本身的硬件方面的限制
- JVM内存大小设置



常见的优化措施：

- nginx的动静分离
- 模板渲染设置缓存，服务上线之后日志等级修改为error或者info
- JVM参数调优
- 数据库调优：将多次的数据库查询处理为一次查询
- 网络IO的改善，高性能RPC的设计
- 服务器硬件的改善：网卡带宽，网络带宽，CPU和内存的提升，IO（高速磁盘的使用）的改善
- 代码优化

## 12 Redis缓存

哪些数据适合放入缓存：

- 即时性数据一致性要求不高的数据
- 访问量大且更新的频率不高（读多写少）

```java
// 使用缓存之后的数据读写伪代码
data = cache.load(id);      // 缓存中读取数据
if (data==null) { 
    data = db.load(id);     // 缓存中没有，从数据库读取
    cache.put(id,data);     // 将数据库查询到的数据写入到缓存
}
return data;                // 返回数据
```

分布式系统的缓存一致性问题：

- 当每一次请求请求到不同服务器时，可能导致缓存用不上的情况
- 以及缓存一致性的问题：在这台服务器更新缓存，但是其他服务器上的缓存没有更新，导致不同的访问请求访问数据不一致的问题

解决缓存不一致的问题：

- 

### 12.1 springboot使用redis

springboot内嵌了redis的starter，加入redis的starter的依赖：

```xml
        <!-- 整合redis作为服务器的缓存中间件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

```

设置redis的配置文件

```
spring:
  redis:
    host: 192.168.50.1
    port: 6379
    
```

简单的redis使用：

- 首先获取到redis的ValueOperations对象，这是一个哈希表对象，有键值的对象，可以使用RedisTemplate或者StringRedisTemplate来构建这个操作对象

- 上面的ValueOperations对象有五种基本类型，获取五种基本类型的方法：`opsForValue()`、`opsForHash()`、`opsForList()`、`opsForSet()`、`opsForZset()`

- 通过上面的ops对象可以进行基本的操作，如新增、查询等，不同的类型的操作对象的操作可能不全相同

  ```java
  ops.append("key","value");      // 基本的redis字符串的键值新增操作
  ops.get("key");                 // 查询键值对应的数据
  ```

一般redis的键是字符串，值是数据json格式化或者对象序列化后的字符串

加入redis之后的业务逻辑：

```java
// 首先需要将从数据库读取数据的操作抽取为一个方法，方便后续的使用
public Map<String,List<CategoryVo>> getCategoryJsonFromDB(Long catalogId) {
    // 从数据库读取并分离出想要的数据的完整逻辑
}

public Map<String,List<Category2Vo>> getCategoryJson(Long catalogId) {
    // 1首先查询redis缓存是否有想要的数据
    String cataJson = redisTemplate.opsForValue("catalogJson");
    // 2如果缓存中没数据，需要查询数据库
    if (cataJson==null) {
        // 3 从数据库中查询到想要的数据
        Map<String,List<Category2Vo>> catalogJsonDB = getCategoryJsonFromDB(catalogId);
        // 4因为redis中存储字符串数据，将对象转换为字符串数据类型
        // 使用Jackson工具转换
        String s = Json.toJsonString(catalogJsonDB);
        // 将数据库查询到的数据写入到缓存汇总
        redisTemplate.opsForValue().set("catalogJson",s);
        // 返回需要的数据
        return catalogJsonDB;
    }
    // 如果缓存中已经有想要的数据了，直接将缓存中的字符串数据转为想要的类型即可
    // 需要使用到Jackson中的TypeReference类,需要传入需要转换的数据类型到这类的泛型参数中
    // 因为构造方法是受保护的，需要自己写一个实现体，因此后面需要加上一个{}
    Map<String,List<Category2Vo>> catalogJson = Json.parseObject(cataJson,new TypeReference<Map<String,List<Category2Vo>>>(){});
    return catalogJson;
}
```

### 12.2 Redis使用的时候对外内存溢出的问题

`OutOfDirectMemoryError`堆外内存溢出

1. springboot使用lettuce作为redis客户端，使用netty进行网络通信
2. lettuce的bug导致netty的堆外内存溢出，因此在这里就使用了Java服务的内存作为netty的堆外内存，但是这样设置始终会出现内存溢出。`-Dio.netty.maxDirectMemory `进行内存的设置
3. 溢出的原因在于堆外内存没有正确的释放

解决方案：

- 不能使用调大内存的操作
- 升级lettuce客户端
- 切换使用jedis的客户端
- 在data-redis的start依赖项中配置exclusion移除lettuce的依赖
- 再加入jedis的依赖

### 12.3 缓存的问题

缓存穿透：缓存中没有，数据库中也没有，大量的null查询都直接流入到数据库中，数据库压力过大

- 缓存空结果
- 布隆过滤器

缓存击穿：缓存中没有，数据库中有，热点数据突然失效，大量并发请求突然来查询这一部分数据

- 设置缓存不过期
- 或者加锁防止突然大量请求查询数据库，加锁需要考虑效率问题

缓存雪崩：缓存中没有，数据库中有，大面积的缓存突然失效，大量的并发请求导致数据库压力过大

- 过期时间随机设置，不要让大批量的缓存过期



加锁的正确做法：

- 同一个锁都可以锁住所有对象，如果单例模式，可以使用`sychronized(this)`加锁
- 加锁的方式：双重逻辑监测，防止出错或者有一些查询没有锁住
- 上述的做法在单服务器模式下可以做到全部锁锁住

```java

```

但是在分布式系统下，有多个服务器的时候，由于不同服务器上的对象不是同一个，因此不同的服务器都不会被锁住，因此会放出不少的线程流向数据库中查询

分布式锁：

- 为了锁住所有的请求，需要采用分布式锁的形式来将所有请求都锁住，整个分布式系统只允许一个查询数据库的请求

分布式锁使用redis的两种实现方法：

- 使用set nx并设置过期时间
- 使用redisson分布式锁框架

### 12.4 set NX实现分布式锁

NX的意思是`not exist`，因此在设置键的时候，需要保证只有一个线程可以设置这个键，因此就可以使用这个命令来保证每次只有一个线程可以来设置访问和设置键值，其他的线程都只能够等待键的删除或者键的过期

`set key value ex nx px`完整的set命令。ex表示过期时间秒数，nx标识不存在才进行设置，XX存在key还是可以设置key，px是超时的毫秒数

RedisTemplate的nx设置方法是使用`setIfAbsent()`不存在才设置，在这个方法可以指定超时时间

```java
// 实际业务的使用过程
// 1. 查缓存，有没有自己想要的数据
String data = RedisTemplate.opsForValue().get("想要的数据的键");
String uuid = UUID.randomUUID();
// 2判断是否存在缓存
if (data==null) {
    // 如果缓存不存在，说明需要查询数据库
    // 3 先判断是否已经有线程在执行查询数据库的操作
    Integer lock = RedisTemplate.opsForValue().setIfAbsent("lock",uuid);
    if (lock!=null) {
        // 如果设置锁成功了，说明需要去查数据库
        // 4 执行查询数据库的操作
        Map<> dataDB = getDataFromDB();
        // 5 查询执行完毕之后需要将数据设置回缓存
        RedisTemplate.opsForValue().set("data",Json.toString(data));
        // 6 设置缓存之后需要删除键
        RedisTemplate.opsForValue().delete("lock");
        return dataDB;
    }else {
        // 如果获取锁失败，需要重新执行上述的操作，进行重试
        return funName();
    }
}
// 如果缓存中有，直接返回缓存中的数据即可
Json.parseObject();
return ;
```

上述就是一个简单的分布式锁的业务逻辑流程，但是上述的业务流程存在较多的问题：

- 主要的就是一个时间的不确定性所导致的很多问题，如果在查询数据库出错，导致最后删除锁的操作没有执行，会导致死锁的出现，缓存中没有数据，且没有其他的线程可以拿到锁并查询想要的数据
- 防出错可以设置异常捕获处理，不管如何都删除锁，或者给锁设置一个过期时间
- 但是设置过期时间还是存在问题：如果在数据库查询过程中，花费了较长时间，这个时候键过期，其他的线程可以进入临界区并进行查询数据库，然后这个时候之前的那个线程查询数据库返回，并将键删除，删除的键不是自己设置的键，还是会出现锁失效问题，出现多次的数据库查询
- 解决就是在删除键的时候判断一下是不是自己的，判断的过程可以使用lua脚本保证原子性
- 此外，对于设置锁和删除锁，一定要保证这两个步骤都是原子性的，不然还是会出现问题（比如突然地断电，导致操作并没有实现，之后恢复了是没有完成想要的操作的一种状态，会导致系统出现问题
- 为了保证原子性的操作，可以使用redis的lua脚本来保证，这整个lua脚本的操作要么都成，要么都失败，是原子的

总得来说，set nx确实可以实现分布式锁，但是存在较多的问题，并且操作起来容易出错，没有考虑周到会导致系统的问题，官方推荐使用redisson来实现分布式锁，这个框架易用稳定。

官方推荐的用法：

1. 设置锁并设置过期时间

```java
SET resource_name my_random_value NX PX 30000
```

2. 使用lua脚本来删除键，确保删除的是自己设置的键

```python
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```



### 12.5 使用redisson分布式锁框架

这个是Java语言可以使用的redis的分布式锁的框架

锁的粒度越小越好，越小越快（就是锁锁住的数据越少越好

快速使用：

- 加入依赖，也可以直接使用springboot给redisson设置的starter

```xml
<dependency>
   <groupId>org.redisson</groupId>
   <artifactId>redisson</artifactId>
   <version>3.16.5</version>
</dependency>  
```

```java
// 1. Create config object
// 使用程序化配置方法
Config config = new Config();
config.useClusterServers()
       // use "rediss://" for SSL connection
      .addNodeAddress("redis://127.0.0.1:7181");
// 单服务器模式
Config config = new Config();
        config.useSingleServer().setAddress("192.168.50.1:6370");
// or read config from file
config = Config.fromYAML(new File("config-file.yaml")); 
// 2. Create Redisson instance

// Sync and Async API
RedissonClient redisson = Redisson.create(config);

// Reactive API
RedissonReactiveClient redissonReactive = redisson.reactive();

// RxJava3 API
RedissonRxClient redissonRx = redisson.rxJava();
// 3. Get Redis based implementation of java.util.concurrent.ConcurrentMap
RMap<MyKey, MyValue> map = redisson.getMap("myMap");

RMapReactive<MyKey, MyValue> mapReactive = redissonReactive.getMap("myMap");

RMapRx<MyKey, MyValue> mapRx = redissonRx.getMap("myMap");
```



```java
// 4. Get Redis based implementation of java.util.concurrent.locks.Lock
RLock lock = redisson.getLock("myLock");

RLockReactive lockReactive = redissonReactive.getLock("myLock");

RLockRx lockRx = redissonRx.getLock("myLock");
// 4. Get Redis based implementation of java.util.concurrent.ExecutorService
RExecutorService executor = redisson.getExecutorService("myExecutorService");

// over 50 Redis based Java objects and services ...
```

Redisson的优点：

```java
        // 获取分布式锁
        RLock lock = redisson.getLock("lock-name");
        // 加锁
        lock.lock();
        try {
            // 在这里执行业务逻辑
            System.out.println("加锁成功，正在执行业务逻辑。。。");
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            // 业务成不成功都需要释放锁
            lock.unlock();
        }
```

- 锁自动续期，有一个watchdog在不断给键续期，这个续期是在lock方法里面实现的，见下方的源码，通过不断的获取锁
- 锁业务完成，锁的过期时间就不会自动续期了，30s的过期时间，如果业务正常执行完毕，会删除锁，如果断电，锁会自动过期，也不会造成死锁的问题

相比较自己去实现锁的写法，这样的一种机制更加方便了我们的使用，就优点类似于JUC包下面的同步器锁的使用一样，使用别人封装好的代码更加简洁。

#### 12.5.1 自动续期的源码

自动续期只有在使用lock不传入参数或者指定leasetime释放时间为-1的时候会进行

默认设置的过期时间：LockWatchdogTimeout，30s，在配置文件`Config.java`中可以修改这个时间的大小

`private long lockWatchdogTimeout = 30 * 1000;`

```java
public RedissonBaseLock(CommandAsyncExecutor commandExecutor, String name) {
    super(commandExecutor, name);
    this.commandExecutor = commandExecutor;
    this.id = commandExecutor.getConnectionManager().getId();
    // 内部设置的锁释放的时间，过期时间
    this.internalLockLeaseTime = commandExecutor.getConnectionManager().getCfg().getLockWatchdogTimeout();
    this.entryName = id + ":" + name;
}
```

`lock.lock(10,TimeUnit.SECONDS)`方法调用并不会执行

自动续期的完整的一个调用链：

`lock` ----->    `lock(-1, null, false);`  ------->    `tryAcquire(-1, leaseTime, unit, threadId);` ------->

`tryAcquireAsync(waitTime, leaseTime, unit, threadId);`  --------->

`scheduleExpirationRenewal(threadId);`     ------->     `renewExpiration();`

```java
// 更新过期时间的方法定义
private void renewExpiration() {
    RedissonBaseLock.ExpirationEntry ee = (RedissonBaseLock.ExpirationEntry)EXPIRATION_RENEWAL_MAP.get(this.getEntryName());
    if (ee != null) {
        // 设置定时任务，传入的参数是一个TimerTask
        Timeout task = this.commandExecutor.getConnectionManager().newTimeout(new TimerTask() {
            public void run(Timeout timeout) throws Exception {
                RedissonBaseLock.ExpirationEntry ent = (RedissonBaseLock.ExpirationEntry)RedissonBaseLock.EXPIRATION_RENEWAL_MAP.get(RedissonBaseLock.this.getEntryName());
                if (ent != null) {
                    Long threadId = ent.getFirstThreadId();
                    if (threadId != null) {
                        RFuture<Boolean> future = RedissonBaseLock.this.renewExpirationAsync(threadId);
                        future.onComplete((res, e) -> {
                            if (e != null) {
                                RedissonBaseLock.log.error("Can't update lock " + RedissonBaseLock.this.getRawName() + " expiration", e);
                                RedissonBaseLock.EXPIRATION_RENEWAL_MAP.remove(RedissonBaseLock.this.getEntryName());
                            } else {
                                if (res) {
                                    RedissonBaseLock.this.renewExpiration();
                                } else {
                                    RedissonBaseLock.this.cancelExpirationRenewal((Long)null);
                                }

                            }
                        });
                    }
                }
            }
        }, this.internalLockLeaseTime / 3L, TimeUnit.MILLISECONDS);
        // 在这里设置了一个定时任务，每过LockWatchdogTimeout的1/3时间就更新一次过期时间
        ee.setTimeout(task);
    }
}
```

更新过期时间的方法：

```java
protected RFuture<Boolean> renewExpirationAsync(long threadId) {
    return evalWriteAsync(getRawName(), LongCodec.INSTANCE, RedisCommands.EVAL_BOOLEAN,
                          "if (redis.call('hexists', KEYS[1], ARGV[2]) == 1) then " +
                          "redis.call('pexpire', KEYS[1], ARGV[1]); " +
                          "return 1; " +
                          "end; " +
                          "return 0;",
                          Collections.singletonList(getRawName()),
                          internalLockLeaseTime, getLockName(threadId));
}
```

不过在实际业务中，一般还是主动设置释放时间更好，不过可以设置为30s，保证业务执行完毕

#### 12.5.2 读写锁

```java
RReadWriteLock lock = redissonClient.getReadWriteLock("  ");      // 获取读写锁
lock.readLock().lock();                            // 读锁
lock.writeLock().lock();                           // 写锁
```

读读不互斥，读写互斥，写写互斥

#### 12.5.4 闭锁CountDownLatch

```java
package com.jiabao.jbmall.product.controller;

import org.redisson.api.RCountDownLatch;
import org.redisson.api.RedissonClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/redisson")
public class RedissonTestController {
    @Autowired
    RedissonClient redisson;

    @GetMapping("/go/{id}")
    @ResponseBody
    public String gogogo(@PathVariable Long id) {
        RCountDownLatch lock = redisson.getCountDownLatch("lock");
        lock.countDown();
        return id+"班放假了";
    }

    @GetMapping("/lock")
    @ResponseBody
    public String lock() {
        RCountDownLatch lock = redisson.getCountDownLatch("lock");
        lock.trySetCount(5);
        try {
            lock.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "都放假了，锁门了";
    }
}
```

#### 12.5.5 信号量Semaphore

```java
@GetMapping("/leave")
@ResponseBody
public String leave() {
    RSemaphore lock = redisson.getSemaphore("park");
    lock.release();
    return "一辆车开走了";
}

@GetMapping("/park")
@ResponseBody
public String park() {
    RSemaphore lock = redisson.getSemaphore("park");
    try {
        lock.acquire();
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
    return "一辆车停进来了";
}
```

### 12.6 分布式锁缓存一致性问题

解决缓存一致性问题：

- 双写模式：先写数据库，再写缓存
  并发下的问题：
  - 线程写数据库，再更新缓存，但是在这个更新缓存之前，另外一个线程执行了写数据库，写缓存的步骤。读到的数据有延迟（是否可以容忍数据的不一致的时间），出现了脏数据（设置过期时间的时候，一定可以保证最终一致性），但是可以保证最终的数据一致性
- 失效模式：先写数据库，再删除缓存
  并发下的问题：
  - 读到之前一个线程还未来得及删除的缓存脏数据

![image-20211205212726967](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E7%BC%93%E5%AD%98%E4%B8%80%E8%87%B4%E6%80%A7%E7%9A%84%E5%8F%8C%E5%86%99%E6%A8%A1%E5%BC%8F.png)

![image-20211205212645976](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E7%BC%93%E5%AD%98%E6%95%B0%E6%8D%AE%E4%B8%80%E8%87%B4%E6%80%A7%E7%9A%84%E5%A4%B1%E6%95%88%E6%A8%A1%E5%BC%8F.png)

面对上述的问题，有一些可以改进的方法：

- 使用分布式的读写锁：读数据等待写数据整个操作完成
- 使用canal：一种MySQL主从复制的模式来实现更新缓存的方式，通过缓存订阅MySQL的binlog，数据变更就更新缓存。利用canal还可以实现数据异构，也就是

实际场景下的一种设计方案：

- 如果是用户维度的数据，由于并发几率不高，不用过多的考虑并发带来的问题，缓存的数据只需要加上过期时间，过一段时间触发读的主动更新就可以避免数据一致性的问题
- 如果是菜单、商品介绍等基础数据，可以使用canal订阅binlog的方式，更新数据库的时候主动触发缓存更新，按照先后来执行这些更新缓存的操作
- 缓存数据+过期时间可以解决大部分业务中对于缓存的要求
- 通过加锁来保证并发的读写、写写的时候按照顺序排队，读读数据就不关心那么多，可以允许，因此这种适合使用分布式下的读写锁的使用



总结：

- 放入缓存中的数据一般本身就不应该是实时性高、一致性要求高的数据。因此缓存+过期时间是比较简单的管理方案，保证每天拿到当前最新数据即可
- 不应该过度设计，增加系统的复杂性
- 遇到实时性一致性要求高的数据，应该查数据库，即使速度慢一点

## 13 SpringCache

简化加入缓存的操作，使用注解就可以直接自动加上缓存的细节逻辑，封装好了的

### 13.1 Cache的使用

使用的步骤：整合SpringCache简化缓存开发

1. 引入依赖：

   ```xml
   <!-- 整合springCache简化缓存开发代码 -->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-cache</artifactId>
   </dependency>
   
   <!-- 整合redis作为服务器的缓存中间件 -->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-data-redis</artifactId>
   </dependency>
   ```

2. 写配置类，一般对于这种整合到springboot-starter中的都是已经做了一部分的自动配置了的。一般自动配置文件是`XXXXAutoConfiguration.java`文件中查看，自动配置文件中配置了`RedisCacheConfiguration`，在这里面自动配置好了缓存换利器`RedisCacheManager`

3. 配置文件还需要指定`spring.cache.type=redis`，指定缓存类型，一般使用redis

4. 在Application类上指定使用cache的注解：`@EnableCaching`

### 13.2 Cache的自动配置和自定义配置

默认配置的默认行为：

- 如果缓存中有，就不会再调用方法来获取数据
- key默认生成的，生成规则是`缓存名::SimpleKey []`
- 缓存的数据的值，使用的是jdk自带的序列化机制，如果缓存的数据中没有实现序列化，将会报错，序列化成功之后才回写到redis
- 默认的过期时间是-1，永不过期

因此在实际的业务逻辑中，不会使用默认配置的行为，而是会自定义：

- 指定生成的缓存的key，在使用缓存的注解中加上key属性指定

  ```java
  @Cacheable(value = {"category"},key="'categoryLevels'")
  key="#root.methodName"key的指定还可以指定表达式;
  #root.targetClass;声明的类名
  /**
  * <ul>
  * <li>{@code #root.method}, {@code #root.target}, and {@code #root.caches} for
  * references to the {@link java.lang.reflect.Method method}, target object, and
  * affected cache(s) respectively.</li>
  * <li>Shortcuts for the method name ({@code #root.methodName}) and target class
  * ({@code #root.targetClass}) are also available.
  * <li>Method arguments can be accessed by index. For instance the second argument
  * can be accessed via {@code #root.args[1]}, {@code #p1} or {@code #a1}. Arguments
  * can also be accessed by name if that information is available.</li>
  * </ul>
  */
  ```

- 指定缓存数据的过期时间：配置文件中修改过期时间

  ```yaml
  spring:
  	cache:
      type: redis
      redis:
        time-to-live: 30     # 单位为毫秒，过期时间
        cache-null-values: true   # 是否缓存null，可以用来防止缓存穿透问题
  ```

- 将缓存的数据保存为json格式
  需要修改缓存管理器
  默认自动配置的原理：

  ​	 `CacheAutoConfiguration`  -> ` CacheConfigurations.getConfigurationClass`确定配置类

  ​	- >   `RedisCacheConfiguration`   

  ​	-> 自动配置了`RedisCacheManage`   

  ​	-> `RedisCacheConfiguration`下的`determineConfiguration()`方法确定到底使用自动配置还是自定义配置）

  ​	 ->  每个缓存决定使用什么配置

  ​	->   如果自定义了redisCacheConfiguration就用已有的，没有就是用默认配置

  ​	->  想要修改缓存配置，只需要自己创建一个`RedisCacheConfiguration`即可

  ​	->  创建自定义缓存之后，就会应用到当前的RedisCacheManager管理的所有缓存分区中

```java
package com.jiabao.jbmall.product.config;

import org.springframework.boot.autoconfigure.cache.CacheProperties;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.serializer.GenericJackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializationContext;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.time.Duration;

// 想要使用某个配置属性类，你可以使用注解将其开启配置属性文件的解析类，然后使用可以直接Autowired注入进来
// 或者直接让其放入方法中，这个方法在容器注入的时候会自动添加进来这一个配置属性类
@EnableConfigurationProperties(CacheProperties.class)
@EnableCaching
@Configuration
public class MyCacheConfiguration {
    @Bean
    public RedisCacheConfiguration redisCacheConfiguration(CacheProperties cacheProperties) {
        // 首先获取默认的Redis缓存配置类
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig();
        // 设置过期时间
        config = config.entryTtl(Duration.ofMillis(30000));
        // 然后指定键、值得序列化构造器
        config = config.serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(new GenericJackson2JsonRedisSerializer()));
        config = config.serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(new StringRedisSerializer()));
        // 加载配置文件中的配置项，下面的这一部分内容是RedisCacheConfiguration的createConfiguration方法中实现的
        // 注使用CacheProperites的配置属性类
        CacheProperties.Redis redisProperties = cacheProperties.getRedis();
        if (redisProperties.getTimeToLive() != null) {
            config = config.entryTtl(redisProperties.getTimeToLive());
        }

        if (redisProperties.getKeyPrefix() != null) {
            config = config.prefixCacheNameWith(redisProperties.getKeyPrefix());
        }

        if (!redisProperties.isCacheNullValues()) {
            config = config.disableCachingNullValues();
        }

        if (!redisProperties.isUseKeyPrefix()) {
            config = config.disableKeyPrefix();
        }
        return config;
    }
}

```

下面是可选的序列化构造器：

- OxmSerializer (org.springframework.data.redis.serializer)
- ByteArrayRedisSerializer (org.springframework.data.redis.serializer)
- GenericToStringSerializer (org.springframework.data.redis.serializer)
- GenericJackson2JsonRedisSerializer (org.springframework.data.redis.serializer)
- FastJsonRedisSerializer (com.alibaba.fastjson.support.spring)
- StringRedisSerializer (org.springframework.data.redis.serializer)
- GenericFastJsonRedisSerializer (com.alibaba.fastjson.support.spring)
- JdkSerializationRedisSerializer (org.springframework.data.redis.serializer)
- Jackson2JsonRedisSerializer (org.springframework.data.redis.serializer)

### 13.3 Cache注解的作用和用法

```java
@Cacheable：触发数据保存到缓存中的操作;
@CacheEvict：缓存失效模式，触发将数据从缓存中删除的操作直接删除缓存中的相关数据;
@CachePut：缓存双写模式，不影响方法执行更新缓存;
@Caching：组合以上多分操作作用到方法上;
@CacheConfig：在类级别共享缓存相关的设置
```

第一个注解用来注解查找方法，后面两个注解用来作用到更新操作

```java
// 如果需要删除多个，可以使用Caching指定多个
// 可以使用三个缓存相关的功能组合
@Caching(evict={@CacheEvict(key = "' '",value="category"),@CacheEvict(key = "' '",value="category")})
@CacheEvict(value="",key="")// 指定需要删除缓存的键值
public void uodateCategory() {
    
}
```

### 13.4 SpringCache的不足

缓存的问题的防治解决：

- 读模式下的一些问题：

缓存穿透：SpringCache可以实现，配置文件中有一个配置可以缓存null值来防治

缓存击穿：默认无加锁的，不能解决缓存击穿，要么手写加锁的业务，要么在@Cacheable注解中加上sync=true的属性

缓存雪崩：大量key过期，解决，加上过期时间，Cache可以设置过期时间，可以防治

- 写模式下的一些问题：springcache没有管理，一般需要自己管理配置

读写加锁：

引入canal：解决缓存一致性问题的一个解决方案，通过伪装成MySQL的一个从数据库来向主数据库申请拉取binlog日志来实现数据同步

读多写多：直接查询数据库

总结：

- 常规数据（读多写少，即时性一致性要求不高的数据）可以使用springcache来简化业务逻辑
- 特殊数据：需要进行特殊设计，不能使用springcache



## 14 异步和线程池

### 14.1 线程创建的几种方式

#### 继承线程类重写run方法

```java
public class ThreadTest {
    public static void main(String[] args) {
        new Thread01().start();
    }
    public static class Thread01 extends Thread {
        @Override
        public void run() {
            // 这里写线程需要执行的任务即可
        }
    }
}
```

#### 实现Runable接口实现run方法

可以直接搭配lambda语法很快的创建一个线程并执行

```java
public class ThreadTest {
    public static void main(String[] args) {
        new Thread(new Runnable()->{
            @Override
            public void run() {
                // 在这里写上线程需要执行的内容即可
            }
        }).srart();
        // 使用lambda表达式简化创建
        new Thread(()->{
            // 写上线程的实现内容即可
        }).srart();
    }
}
```

#### 实现Callable接口并实现call方法

相比其他两种，这种方式的优点就是可以获取线程执行的结果，但是这个执行结果的获取是异步阻塞的

此外这种实现方式需要使用FutureTask进行封装，这个FutureTask实质上也是一个线程类的子类

```java
public class ThreadTest {
    public static void main(String[] args) {
        FutureTask<Integer> task = new FutureTask<Integer>(new Callable()->{
            @Override
            public Integer call() {
                // 在这里写上线程需要执行的内容即可
            }
        });
        // 使用lambda表达式简化创建
        FutureTask<Integer> task1 = new FutureTask(()->{
            // 写上线程的实现内容即可
        });
        new Thread(task).start();
        task.get();   // 获取结果，需要捕获异常，此外还会在这里阻塞线程
    }
}
```

#### 使用线程池

 相比之前的三种创建线程的方式，优点在于这种方式可以控制资源，防止线程创建过多导致系统崩溃

Executors底下有好好几个静态工厂方法可以用来获取线程池

```java
ExecutorService service = Executors.newFixedThreadPool(10);
ExecutorService executorService1 = Executors.newCachedThreadPool();
ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(10);
ScheduledExecutorService scheduledExecutorService1 = Executors.newSingleThreadScheduledExecutor();    // 执行定时任务的
ExecutorService executorService2 = Executors.newSingleThreadExecutor();
```

#### ThreadPollExecutor

这个类的执行顺序和线程的运行流程：

1. 创建线程池，然后线程池对象根据传入的构造器参数中的核心线程数创建这么多的线程准备接受任务，核心线程数是一直保持在线程池中的

2. 如果核心线程池满了都被占用在执行任务，这个时候就需要创建新的线程来继续接受任务并执行。需要保证创建的线程数量不超过最大的线程数量

3. 如果最大线程数量的线程都被占用正在执行任务，这个时候还有任务进来的话就会将任务添加到工作队列（阻塞队列）等待线程执行完毕再占有线程并执行任务

4. 如果设置的阻塞队列也被占满了，这个时候就需要拒绝后续任务的添加，这个时候选择的拒绝策略就会发挥作用

5. 根据设置的拒绝策略来拒绝任务的添加：拒绝策略有

   - `ThreadPoolExecutor.AbortPolicy`：默认的拒绝策略是直接抛弃任务，一旦拒绝就会抛出异常`RejectedExecutionException`
   - `ThreadPoolExecutor.CallerRunsPolicy`：不抛弃任务，让这些任务在创建该线程的线程中同步执行，但是这种方法可以会减缓任务加入队列的速度

   - `ThreadPoolExecutor.DiscardPolicy`：丢弃任务不抛出异常
   - `ThreadPoolExecutor.DiscardOldestPolicy`：丢弃队列中最早进来的任务

6. 如果工作队列为空，这个时候如果还有空闲的线程，一旦线程空闲时间超过了KeepAliveTime,就会对最大线程数量减去核心线程数量的这部分线程进行释放

![image-20211210232557437](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E8%87%AA%E5%BB%BA%E7%BA%BF%E7%A8%8B%E6%B1%A0%E7%9A%84%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B.png)

corl：7，max：20，Queue：50，任务：100怎么分配，流程？

- 7个任务立即执行
- 然后执行任务进入队列的操作
- 然后再进入13个任务到线程池中创建线程执行
- 剩下的30个根据选择的拒绝策略来决定如何操作



此外还有一个ThreadPollExecutor类可以用来创建线程池

这个创建线程池类的几个构造函数参数：

```java
// 构造器函数
public ThreadPoolExecutor
    (int corePoolSize,  // 核心线程数，定义了最小同时运行的线程数量（队列不满时）
     int maximumPoolSize, // 队列任务达到容量时，可以运行的最大线程数（队列满了就增加线程数量）
     long keepAliveTime,  // 线程数量超过核心线程数，等待一定时间还没有使用就销毁该线程
     TimeUnit unit,       // keepAliveTime的时间单位
     BlockingQueue<Runnable> workQueue,  // 达到核心线程数时，任务加入此队列
     ThreadFactory threadFactory,        // 创建新线程时用到,线程工厂
     RejectedExecutionHandler handler)  // 队列饱和时的拒绝策略（饱和策略）
```



```java
// 下面的四种是线程池的拒绝策略
/**
 * <ol>
 *
 * <li>In the default {@link ThreadPoolExecutor.AbortPolicy}, the handler
 * throws a runtime {@link RejectedExecutionException} upon rejection.
 *
 * <li>In {@link ThreadPoolExecutor.CallerRunsPolicy}, the thread
 * that invokes {@code execute} itself runs the task. This provides a
 * simple feedback control mechanism that will slow down the rate that
 * new tasks are submitted.
 *
 * <li>In {@link ThreadPoolExecutor.DiscardPolicy}, a task that
 * cannot be executed is simply dropped.
 *
 * <li>In {@link ThreadPoolExecutor.DiscardOldestPolicy}, if the
 * executor is not shut down, the task at the head of the work queue
 * is dropped, and then execution is retried (which can fail again,
 * causing this to be repeated.)
 *
 * </ol>
 */
```

### 14.2 异步任务

#### 异步任务的创建

两种方法：

- runAsync()参数可以传入Runnable和线程池，后面的线程池参数可以传也可以不传。这个方法创建的异步任务是没有返回值的
- supplyAsync()参数可以传入Supply<>和线程池，线程池参数可以不传递。这个方法创建的异步任务是有返回值的

方法是不是具有返回值可以查看创建之后的ConpletableFuture的泛型参数，如果是Void标识没有返回值

#### 异步任务编排

编排是指安排不同任务之间的执行顺序或者执行逻辑：

- 如果需要在某个任务执行之后，可以使用下面的方法
  - thenAcceptAsync()：接受Consumer<>和线程池参数，无返回值
  
  - thenApplyAsync()：接受Function<>和线程池参数，有返回值
  
  - whenComplete()，可以传入的BiConsumer<>和线程池参数，BiConsumer<>可以接受两个参数，上一步结果的返回值和上一步结果操作是否存在异常
  
  - handler()：可接受的参数BiFunction<>(接收两个参数)和线程池,有返回值
  
- 两个任务都要执行成功之后才执行：
  
  - runAfterBothAsync()：组合两个future，不需要获取future结果，两个future完成之后，执行之后的任务
  - thenAcceptBothAsync()：组合两个future，需要获取future结果，无返回值
  - thenCombineAsync():组合两个future，需要获取future结果，有返回值
  
- 两个任务中的一个完成
  - runAfterEitherAsync()：组合两个任务，只要其中一个完成就可以执行后续任务，不用获取任务执行结果，无返回值
  - acceptEitherAsync()：组合两个任务，只要其中一个完成就可以执行，需要获取future结果，无返回值
  - applyToEitherAsync()：组合两个任务，其中一个完成就可，需要获取future结果，有返回值
  
- 组合多个任务：
  - allOf()：多个任务都完成才执行后续任务
  - anyOf()：多个任务只要有一个完成就可以执行后续任务



## 15 消息队列

使用场景：

- 异步处理：用户注册处理，就可以应用消息队列，注册成功消息写入消息队列，后面其他需要进行的服务去消费消息即可
- 应用解耦：
- 流量控制：
