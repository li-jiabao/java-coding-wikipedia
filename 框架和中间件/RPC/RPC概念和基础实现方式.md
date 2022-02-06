# RPC

## 1. 什么是 RPC

`RPC（Remote Procedure Call）远程过程调用`，它是一种<u>通过网络从远程计算机程序上请求服务，而不需要了解底层网络技术的协议</u>，RPC是一种技术概念，并不是一种框架。比如两个不同的服务 A、B 部署在两台不同的机器上，如果A想要调用B的方法，就可以使用RPC，可以远程调用其他机器上的方法，而且可以很方便快捷。

## 2. RPC 核心原理

我们可以将整个 RPC的 核心功能看作是下面 6 个部分实现的：

- **客户端（服务消费端 Client）** ：调用远程方法的一端。
- **服务端（服务提供端 Server）** ：提供远程方法的一端。
- **客户端 Stub（桩）** ： 这其实就是一<u>代理类</u>。代理类主要做的事情很简单，就是把你调用方法、类、方法参数等信息传递到服务端。
- **服务端 Stub（桩）** ：这个桩就不是代理类了。这里的服务端 Stub 实际指的就是接收到客户端执行方法的请求后，去指定对应的方法然后返回结果给客户端的类。
- **网络传输** ： 网络传输就是把你调用的方法的信息比如说参数这些东西传输到服务端，然后服务端执行完之后再把返回结果通过网络传输给你传输回来。网络传输的实现方式有很多种比如最基本的 Socket 或者性能以及封装更加优秀的 Netty（推荐）。可以采用TCP/IP协议的socket来进行，但是比较底层，比较麻烦，也可以使用http，但是传输通信的速度比较慢。

其实RPC的核心本质：一台机器调用另外一台机器的方法并获取结果。

A机器将自己想要调用的方法和对应的内容通过网络进行传输（实质上二进制数据的传输），然后B机器接收到这部分数据并解析出想要调用的方法并执行，然后再将结果通过网络传输回A机器

**核心原理如下**：

![](https://gitee.com/veal98/images/raw/master/img/20201126202743.png)

1. 服务消费方（Client）以本地调用方式调用服务；
2. Client Stub 接收到调用后，负责将方法、参数等组装成能够进行网络传输的消息体 `RpcRequest`（需要进行序列化）；
3. Client Stub 找到服务地址，并将消息发送到服务端；
4. Server Stub 收到消息体后，反序列化为 Java 对象 `RpcRequest`；
5. Server Stub 根据 `RpcRequest` 中的类、方法、方法参数等信息调用本地的服务/方法；
6. 本地服务执行并将结果返回给 Server Stub；
7. Server Stub 得到方法执行结果并将组装成能够进行网络传输的消息体 `RpcResponse`（需要进行序列化）发送至消费方；
8. Client Stub 接收到消息体，并反序列化为 Java 对象 `RpcResponse` 。这样，服务消费方得到了最终结果。

时序图如下：

![image-20211101203614729](https://gitee.com/Jia_bao_Li/img/raw/master/img/RPC%E6%97%B6%E5%BA%8F%E5%9B%BE.png)

## 3 简单RPC实现

在下面的这些实现中其实都没有使用到序列化和反序列化，都是通过简单的字节数组传输简单的数据然后再利用这些数据自己处理了一部分内容，如果为了方便，可以直接使用序列化和反序列化框架

- JDK自带的序列化和反序列化，就是ObjectInputStream和ObjectOutputStream
- Hessian序列化框架
- Jakson的json转换框架
- xml文件的转换
- ...其他的一些序列化框架，有的快有的慢

### 3.1 第一版

通信的逻辑和协议由客户端和服务端提前商量好。

- 客户端将所有的通信传输过程代码全部自己实现
- 服务端根据客户端传输过来的数据进行解析执行并返回结果

缺点：

- 一旦业务逻辑改变，基本全部都需要做改变（客户端服务端都需要进行修改）

```java
interface IUserService {
    public User findUserByID(int ID){};
}

class User {
    private int ID;
    private String name;
    
    public User(int ID,String name) {
        this.ID=ID;
        this.name=name;
    }
}

class IuserServiceImpl {
    @Override
    public User findUserByID(int ID) {
        return new User(ID,"Lilei");
    }
}

import com.jiabao.rpc.common.IUserServiceImpl;
import com.jiabao.rpc.common.User;

import java.io.*;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {

    private static boolean running = true;
    public static void main(String[] args) throws IOException {
        // 创建ServerSocket对象监听8888端口
        ServerSocket ss = new ServerSocket(8888);
        while (running) {
            // 获取连接的Socket对象
            Socket s = ss.accept();
            process(s);
            s.close();
            running=false;
        }
        ss.close();
    }
	// 服务端实现的处理逻辑
    public static void process(Socket ss) throws IOException {
        // 获取socket对象的输入输出流
        InputStream in = ss.getInputStream();
        OutputStream out = ss.getOutputStream();
        // 将socket的输入输出流传输到data输入输出流进行读取处理
        DataInputStream dataInputStream = new DataInputStream(in);
        DataOutputStream dataOutputStream = new DataOutputStream(out);

        int id = dataInputStream.readInt();
        User user = new IUserServiceImpl().findUserByID(id);
        // 将处理之后的结果通过输出流发送出去
        dataOutputStream.writeInt(user.getID());
        dataOutputStream.writeUTF(user.getName());
        // flush从缓冲区刷出才可以进行传输，没有执行这一步将不会看到结果
        dataOutputStream.flush();
    }
}

import com.jiabao.rpc.common.User;
import java.io.*;
import java.net.Socket;

public class Client {
    public static void main(String[] args) throws IOException {
        // 创建一个socket对象用来连接服务器和服务器进行通信
        Socket ss = new Socket("127.0.0.1",8888);
        // 使用字节数组输出流来进行基础的二进制数据的传输
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        // 增强版的字节流，可以直接传输基础类型数据
        DataOutputStream out = new DataOutputStream(byteArrayOutputStream);

        out.writeInt(123);
        // socket对象获取输出流并向里面写入刚刚传进来的数字转换而成的字节数组
        ss.getOutputStream().write(byteArrayOutputStream.toByteArray());
        // 将输出流刷入socket并传输
        ss.getOutputStream().flush();

        // 获取服务器传输过来的数据,获取socket的输入流读取服务器传输的内容
        DataInputStream inputStream = new DataInputStream(ss.getInputStream());

        // 读取服务器传输过来的对象的ID和name
        // 这里并没有使用序列化和反序列化的方式直接传输对象
        // 传输对象可以直接使用ObjectInputStream这一类的输入输出流
        int id = inputStream.readInt();
        String name = inputStream.readUTF();
        User user = new User(id,name);

        System.out.println(user);
        out.close();
        ss.close();
    }
}

```

### 3.2 第二版：静态代理

```java
package com.jiabao.rpc.rpc02;

import com.jiabao.rpc.common.IUserService;
import com.jiabao.rpc.common.User;

import java.io.ByteArrayOutputStream;
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.net.Socket;

public class Stub implements IUserService {

    @Override
    public User findUserByID(int ID) throws IOException {
        // 创建一个socket对象用来连接服务器和服务器进行通信
        Socket ss = new Socket("127.0.0.1",8888);
        // 使用字节数组输出流来进行基础的二进制数据的传输
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        // 增强版的字节流，可以直接传输基础类型数据
        DataOutputStream out = new DataOutputStream(byteArrayOutputStream);

        out.writeInt(ID);
        // socket对象获取输出流并向里面写入刚刚传进来的数字转换而成的字节数组
        ss.getOutputStream().write(byteArrayOutputStream.toByteArray());
        // 将输出流刷入socket并传输
        ss.getOutputStream().flush();

        // 获取服务器传输过来的数据,获取socket的输入流读取服务器传输的内容
        DataInputStream inputStream = new DataInputStream(ss.getInputStream());

        // 读取服务器传输过来的对象的ID和name
        int id = inputStream.readInt();
        String name = inputStream.readUTF();
        User user = new User(id,name);

        out.close();
        ss.close();
        return user;
    }
}

```

### 3.3 第三版：动态代理

```java
package com.jiabao.rpc.rpc03;

import com.jiabao.rpc.common.IUserService;
import com.jiabao.rpc.common.IUserServiceImpl;
import com.jiabao.rpc.common.User;

import java.io.ByteArrayOutputStream;
import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.IOException;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.net.Socket;

public class Stub {
    public static Object getStub(Class clazz) {
        // 创建以代理类对象需要传入的调用处理器
        // 调用处理器，需要创建一个调用处理器类实例，这个实例需要重写invoke方法
        // 重写是为了指定在调用绑定了这个处理器的接口中的方法时候的一些处理逻辑
        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                // 创建一个socket对象用来连接服务器和服务器进行通信
                Socket ss = new Socket("127.0.0.1", 8888);
                // 使用字节数组输出流来进行基础的二进制数据的传输
                ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
                // 增强版的字节流，可以直接传输基础类型数据
                DataOutputStream out = new DataOutputStream(byteArrayOutputStream);

                out.writeInt((Integer) args[0]);
                // socket对象获取输出流并向里面写入刚刚传进来的数字转换而成的字节数组
                ss.getOutputStream().write(byteArrayOutputStream.toByteArray());
                // 将输出流刷入socket并传输
                ss.getOutputStream().flush();

                // 获取服务器传输过来的数据,获取socket的输入流读取服务器传输的内容
                DataInputStream inputStream = new DataInputStream(ss.getInputStream());

                // 读取服务器传输过来的对象的ID和name
                int id = inputStream.readInt();
                String name = inputStream.readUTF();
                User user = new User(id, name);

                out.close();
                ss.close();
                return user;
            }
        };
        // 使用Proxy的静态工厂模型创建实例，根据传入的参数和指定的处理器返回相对应的代理类
        Object proxy = Proxy.newProxyInstance(clazz.getClassLoader(),new Class[]{clazz},handler);
        return proxy;
    }
}

```

## 4. 常见的 RPC 框架

我们这里说的 RPC 框架指的就是可以**让客户端直接调用服务端方法，就像调用本地方法一样简单**的框架：

- **RMI（JDK自带）：** JDK 自带的 RPC，有很多局限性，不推荐使用。
- **Dubbo:** Dubbo 是阿里巴巴公司开源的一个高性能优秀的服务框架，使得应用可通过高性能的 RPC 实现服务的输出和输入功能，可以和 Spring 框架无缝集成。目前 Dubbo 已经成为 Spring Cloud Alibaba 中的官方组件。
- **gRPC** ：gRPC是可以在任何环境中运行的现代开源高性能 RPC 框架。它可以通过可插拔的支持来有效地连接数据中心内和跨数据中心的服务，以实现负载平衡，跟踪，运行状况检查和身份验证。它也适用于分布式计算的最后一英里，以将设备，移动应用程序和浏览器连接到后端服务。
- **Hessian：** Hessian 是一个轻量级的 remotingonhttp 工具，使用简单的方法提供了 RMI 的功能。 相比WebService，Hessian更简单、快捷。采用的是二进制 RPC 协议，因为采用的是二进制协议，所以它很适合于发送二进制数据。
- **Thrift：** Apache Thrift 是Facebook开源的跨语言的 RPC 通信框架，目前已经捐献给Apache基金会管理，由于其跨语言特性和出色的性能，在很多互联网公司得到应用，有能力的公司甚至会基于 thrift 研发一套分布式服务框架，增加诸如服务注册、服务发现等功能。

## 5. RPC 与 HTTP

🚨 RPC 与 HTTP 并非一个层级的概念：

- **RPC 只是一种概念、一种设计**，就是为了解决 **不同服务之间的调用问题**, 它一般会包含有 **传输协议** 和 **序列化协议** 这两个。

- **HTTP 是一种协议**，<u>RPC 框架可以使用 HTTP 协议作为传输协议或者直接使用 TCP 作为传输协议</u>，使用不同的协议一般也是为了适应不同的场景。