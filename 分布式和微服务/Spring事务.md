# Spring中的事务

@Transactional注解标注当前的方法中所有数据库操作属于同一个事务中的操作，一旦其中某个操作失败就会触发回滚

使用该注解可能出现的问题：

- 如果包裹的方法操作耗时很长，可能会导致一直占用数据库连接，在高并发的场景下面就会出现大问题，比如数据库连接不够的情况。这种耗时很长的事务叫做长事务：运行时间较长、长时间未能提交的事务

长事务的问题：

- 数据库连接池被占满，应用无法在获取数据库连接资源
- 引发数据库的死锁
- 数据库回滚操作耗时长
- 主从延时变大

避免长事务：

- 对一个长事务进行拆分，大的事务拆分为多个小事务，变快，减小粒度

使用@Transaction声明的事务称为生命式事务：

- 使用简单
- 但是声明式事务的粒度是整个方法，如果事务方法设计不得当，可能导致事务粒度很大，出现上面的长事务问题
- 此外对于事务的控制不能够更加精细化

编程式事务：

- 基于地层API，程序员自己去设计事务开启、提交和回滚等操作
- SPring中可以使用TransactionTemplate进行事务的操作手动设计事务控制事务

```java
@Autowired 
private TransactionTemplate transactionTemplate; 
 
... 

public void save(RequestBill requestBill) { 
    transactionTemplate.execute(transactionStatus -> {
        requestBillDao.save(requestBill);
        //保存明细表
        requestDetailDao.save(requestBill.getDetail());
        return Boolean.TRUE; 
    });
} 
```

如果想使用@Transactional注解使用声明式事务，就需要仔细的设计方法的粒度，尽量将不需要在事务中执行的操作溢出，将整个的事务操作粒度控制好。或者将某些事务操作封装为一个方法，在需要进行事务操作的位置进行调用

```java
@Service
public class OrderService{

    public void createOrder(OrderCreateDTO createDTO){
        query();
        validate();
        saveData(createDTO);
    }
  
  //事务操作
    @Transactional(rollbackFor = Throwable.class)
    public void saveData(OrderCreateDTO createDTO){
        orderDao.insert(createDTO);
    }
}
```

不过使用这种方式可能会出现事务失效的情况：因为注解的生效是通过AOP实现，需要生成AOP代理再去调用，直接在类中方法调用使用的是原始对象，不能生效。还有常见的不生效场景：

- 注解应用在非public修饰的方法
- 注解属性propagation设置错误
- 同一个类中直接调用方法
- 异常被捕获，而不能正常进行事务的回滚机制

实际使用应该将事务方法单独放入另外一个类，比如新增一个manager层，然后spring注入使用。主要有下面两种方式

1. 创建一个manager层的事务管理类，然后在需要使用该事务的位置注入该manager

```java
@Service
public class OrderService{
  
    @Autowired
   private OrderManager orderManager;

    public void createOrder(OrderCreateDTO createDTO){
        query();
        validate();
        orderManager.saveData(createDTO);
    }
}

@Service
public class OrderManager{
  
    @Autowired
   private OrderDao orderDao;
  
  @Transactional(rollbackFor = Throwable.class)
    public void saveData(OrderCreateDTO createDTO){
        orderDao.saveData(createDTO);
    }
}
```

2. 启动类中添加`@EnableAspectJAutoProxy(exposeProxy=true)`，方法内使用`AopContext.currentProxy()`获取代理类，再使用事务

```java
SpringBootApplication.java

// 启动类中加入注解
@EnableAspectJAutoProxy(exposeProxy = true)
@SpringBootApplication
public class SpringBootApplication {}

OrderService.java
  
public void createOrder(OrderCreateDTO createDTO){
    // 获取代理对象
    OrderService orderService = (OrderService)AopContext.currentProxy();
    orderService.saveData(createDTO);
}
```

