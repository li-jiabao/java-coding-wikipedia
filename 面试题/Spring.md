# Spring

## IOC控制反转

将对象创建的过程交给其他容器来进行管理，而不是交给程序员自己借助new来创建。这就类似与将某些事情交给第三方来做，控制权交给了第三方。

## Spring的IOC容器

- BeanFactory：IOC容器接口，一般不给开发人员使用的
- ApplicationContext：IOC容器接口，给开放人员使用的，继承了BeanFactory，新增了一些加强方法

两个容器都可以通过XML配置，支持属性的自动注入，通过getBean方法获取Bean对象

两个容器的区别：

- BeanFactory：创建bean实例的时机，在用户第一次调用getBean的时候才去创建实例对象，不支持国际化i18n
- ApplicationContext：应用程序启动的时候就会实例化bean，不会等到使用的时候才创建，支持国际化
- ApplicationContext可以将事件发布到注册为监听器的bean
- BeanFactory一般用于测试非生产使用，提供了一些基本的容器功能，ApplicationContext提高了更多的高级功能。生产的时候使用ApplicationContext的优先级更高

