---
title: 设计模式之代理模式
author: lijiabao
date: 2021-05-27 23:30
categories:"Design Pattern"
tags: "Design Pattern"
---

介绍一下设计模式中的比较常用，应用广泛的一种设计模式——代理模式

# 代理模式

代理模式实际上就是在客户和目标对象之间增加一个代理对象，让客户对目标的直接访问变成客户先访问代理再通过代理取访问目标对象，然后再将获取的信息返回给客户。访问的底层逻辑和结果的处理逻辑都在代理中实现，客户只需要关心怎么使用就可以，这样对于之后的底层逻辑修改只需要在代理中进行就可以了，而不需要在客户上进行修改。

远程代理：通过客户本地设置的代理来通过网络访问位于另外一个主机上的目标对象，客户只需要调用本地方法，这个本地方法通过代理来对目标对象完成访问，对于底层的网络通信访问逻辑和访问时所进行的操作逻辑都在代理中实现。

下面将以租房以及租房中介为例来介绍一下静态代理和动态代理的实现

## 静态代理

静态代理的实现：

```java
// 抽象对象
public interface RentInterface {
    public void rent();
}

// 创建一个房东对象，是客户访问的目标对象
public class Host implements RentInterface {
    @Override
    public void rent(){
        System.out.println("房东出租房屋");
    }
}

// 创建一个代理对象，用来代理房东的事务
public class Agent implements RentInterface {
    // 在代理中创建一个被代理类的字段
    private Host host;
    
    public Agent() {}
    
    public Agent(Host host) {this.host = host;}
    
    @Override
    public void rent() {
        host.rent();
    }
    
    public void checkHouse() {
        System.out.println("带客户看房");
    }
    public void chargeFromClient() {
        System.out.println("中介收费");
    }
    
    public void payToHost() {
        System.out.println("中介给房东支付租金");
    }
    
    public void giveKeyToClient() {
        System.out.println("中介把房子钥匙给客户");
    }
}

// 创建一个客户对象，这个对象用来看房租房
public class Client {
	public static void main(String[] args) {
        Host host = new Host();
        Agent agent = new Agent(host);
        // 中介发布租房信息
        agent.rent();
        // 带客户看房
        agent.checkHouse();
        // 如果客户满意，收费，支付租金给房东，把钥匙给客户
        if (client.isSatisfied()) {
            agent.chargeFromClient();
            agent.payToHost();
            agent.giveKeyToClient();
        // 客户不满意，更换下一家
        else
            agent.changeHouse();
    }
}
```

## 动态代理

动态代理的实现：需要使用Java自带的功能，使用`InvocationHandler`接口和`Proxy`类来完成

```java
// InvocationHandler接口，内部有一个invoke方法
public Interface InvocationHandler {
    public Object invoke(Object proxy, Method method, Object[] args);
    // 由三个参数，代理类的实例，方法名，方法传递的参数列表
}

// Proxy类下有一个newProxyInstacne静态方法，用来获取代理类的实例
// 这个方法有三个参数，类加载器，类接口列表，调用处理器对象
public static Object newProxyInstance(ClassLoader loader,
                                          Class<?>[] interfaces,
                                          InvocationHandler h) {}
```

上述代码改用Proxy类来定义动态代理：

```java
// 只修改代理类即可
public class Agent implements InvocationHandler {
    private Object target;
    
    public Agent() {}
    
    public Agent(Object target) {
        this.target = target;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) {
        Object result = method.invoke(target, args);
        return result;
    }
    // 获取代理Proxy对象
    public Object getProxy() {
        return Proxy.newProxyInstance(
            this.getClass.getClassLoader(),
            this.getClass().getInterface(),
            this)
    }
}

// 测试
public class Client {
    public static void main(String[] args) {
        Host host = new Host();
        Agent agent = new Agent(host);  // 创建实现了Invocation方法的类实例并传入需要被代理的类
        Object proxy = agent.getProxy();  // 获取代理
        proxy.rent();  // 代理调用被代理类的租房方法
    }
}
```

## 静态代理和动态代理的优缺点

### 静态代理的优点

- 可以让被代理的类只关注自己的代码实现逻辑和功能实现，而不需要关注和外界的交互
- 被代理对象和外界的交互交予代理类来做
- 当与外界的交互方式需要发生改变时可以只修改代理类即可完成，维护和更新更加方便简洁

### 静态代理的缺点

- 增加了一个代理类，导致代码增加，开发效率降低

### 动态代理的优点

- 静态代理的优点动态代理也拥有
- 动态代理代理的是一类接口，因此可以代理多个实现了该接口的类
- 减少了每个类都创建代理对象，减少了代码量