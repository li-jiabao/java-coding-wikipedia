# Netty

## 1 Netty概述

### 1.1 Netty介绍

由于NIO同步非阻塞式的网络编程比较麻烦，为了简化网络编程，因此出现Netty网络编程的框架。

Netty是一个高性能的、异步事件驱动的NIO框架，基于JavaNIO的API实现，提供了对TCP、UDP和文件传输的支持。netty所有的IO操作都是异步非阻塞的。用来快速开发高性能、可扩展协议的服务器和客户端

Netty支持多种网络协议，FTP、HTTP、SMTP、TCP\IP等

利用Future-Listenner的机制，用户可以主动获取或者利用通知机制获取IO操作结果。

### 1.2 Netty特点

- 设计：
  - 针对多种传输类型的协议实现了统一的接口
  - 简单强大的线程模型（Ractor线程模型）
  - 多路复用
- 性能：
  - 较原生的Java API更好的吞吐量，较低的延时
  - 共享池和重用技术（使得资源损耗更小）
  - 零拷贝技术减少了内存拷贝次数
- 健壮性：
  - 相比NIO，发生异常（内存溢出、死循环等）更少

### 1.3 Netty的用途

- 使用Netty实现RPC网络通信工具，用在分布式系统的不同系统之间的相互调用
- 创建一个HTTP服务器，类似于Tomcat。设计处理基本的HTTP请求即可
- 实现即时通信系统：类似于微信的系统
- 消息推送系统
- 很多的开源框架都是基于Netty实现的（Dubbo、gRPC、Elastic Search等）
- 互联网游戏的设计

## 2 Netty原理

### 2.1 多路复用技术

IO编程中，需要同时处理多个客户端接入请求时，可以利用多线程或者IO多路复用技术进行处理。

IO多路复用技术把多个IO的阻塞复用到同一个select/poll/epoll的阻塞，从而使得系统在单线程下可以同时处理多个客户端请求。

和传统的多线程/多进程模型相比，IO多路复用的优点：

- 系统开销小，不需要创建额外的进程或者线程
- 不需要维护进程或这线程，降低了维护的开销，节省了系统的资源

IO多路复用技术：Linux下面有select、poll和epoll这三种技术



Netty的<font color='red'>IO线程NioEventLoop由于聚合了多路复用器Selector</font>，可以同时并发处理很多个的客户端channel，读写操作非阻塞，可以提高IO线程的运行效率，避免IO造成线程阻塞。

### 2.2 Ractor线程模式

Ractor模式使用的异步非阻塞IO，所有的IO操作不会阻塞，一个线程可以处理所有的IO相关操作

#### 2.2.0 Ractor的基本形态

![image-20211028172532784](https://gitee.com/Jia_bao_Li/img/raw/master/img/Racttor%E5%8D%95%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%BC%8F.png)

#### 2.2.1 Ractor单线程模式

所有的IO操作都在同一个NIO线程上面完成，NIO线程职责：

- 服务端：接收客户端TCP连接
- 客户端：负责发送TCP连接请求
- 读取通信对端的请求或者应答消息
- 向通信对端发送请求或者应答消息

![image-20211028172715063](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E5%8D%95%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%BC%8F.png)

缺点：

- 只有一个线程，可以操作的数量优先，并发量上来就会导致大量阻塞

#### 2.2.2 Ractor多线程模式

和单线程的区别就是使用了一组NIO线程处理IO操作。

组成：

- `NIO线程-Acceptor`：监听服务端，接收客户端的TCP连接请求
- `handler`：负责响应除了连接之外的其他事件
- `NIO线程池`：负责网络IO的读、写。线程池可以使用Java线程池，负责消息读取、解码和发送

![image-20211028172737672](https://gitee.com/Jia_bao_Li/img/raw/master/img/Ractor%E5%A4%9A%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%BC%8F.png)

优点：

- 可以充分发挥多核CPU的处理能力

缺点：

- 多线程数据共享和访问比较复杂
- Ractor单线程处理所有事件的监听和响应，容易在高并发下遇到瓶颈

#### 2.2.3 Ractor主从多线程模式

用来接收客户端连接不再是单独的NIO线程，而是NIO线程池，为了解决单线程ractor的性能瓶颈

- 一个主线程：Acceptor用来接收客户端的TCP连接，当连接请求处理完成后（接入认证），将新创建的SocketChannel注册到IO线程池（从线程池）只处理连接请求，其他的事件请求交给其他的子ractor线程处理
- subractor负责调度handler处理后面的事件，然后handler调用对应work线程处理事件
- 从IO线程池：负责SocketChannel的读写和编解码工作
- 需要设定负载均衡策略：防止某一个线程的压力过大

![image-20211028172804766](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E4%B8%BB%E4%BB%8ERactor%E5%A4%9A%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%BC%8F.png)

### 2.3 netty通信时序

服务端通信时序：

![image-20211101210858436](https://gitee.com/Jia_bao_Li/img/raw/master/img/netty%E6%9C%8D%E5%8A%A1%E7%AB%AF%E9%80%9A%E4%BF%A1%E6%97%B6%E5%BA%8F.png)

客户端通信时序：

![image-20211101205907839](https://gitee.com/Jia_bao_Li/img/raw/master/img/netty%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%97%B6%E5%BA%8F%E5%9B%BE.png)

### 2.4 IO读写技术

下面的图展示了IO的三种读写技术：

- 直接IO技术，IO在内核空间和用户空间传递
- 内存映射文件技术，将内核缓冲区直接映射成一个进程的文件
- **零拷贝技术**：将内核缓冲区和Socket缓冲区也进行映射

衍生出来的几种技术主要的目的：减少IO结果被复制的次数，减少内存拷贝次数，提升效率

![image-20211028170421815](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E7%9B%B4%E6%8E%A5IO.png)

![image-20211028170441549](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E5%86%85%E5%AD%98%E6%98%A0%E5%B0%84%E6%96%87%E4%BB%B6.png)

![image-20211028170504470](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E9%9B%B6%E6%8B%B7%E8%B4%9D%E6%8A%80%E6%9C%AF.png)

1. netty的零拷贝技术使用了<font color='red'>堆外的直接内存</font>，不需要进行字节缓冲区的二次拷贝。使用传统堆内内存，JVM首先需要将堆内存Buffer拷贝一份到直接内存，然后再进行写入操作，直接使用堆外内存，就<font color='red'>节省了一次缓冲区的内存拷贝</font>
2. netty提供了组合Buffer对象，可以聚合多个ByteBuffer对象，可以和操作一个Buffer一样，避免了传统的内存拷贝合并大buffer
3. transferTo()方法可以直接将文件缓冲区的数据发送到目标channel，避免了传统循环write方式的内存拷贝问题

[详细的零拷贝技术请查看这篇文章](./零拷贝技术.md)

### 2.5 内存池技术

一种缓冲区重用技术

缓冲区的内存回收比较麻烦耗时

### 2.6 无锁设计

Netty采用了串行无锁化设计，在IO线程内部进行串行操作，避免多线程竞争导致的性能下降

Netty的NioEventLoop读取到消息之后，直接调用ChannelPipeline的fireChannelRead(Object msd)，只有用户补主动切换线程，一直会由NioEventLoop调用到用户的Handler，期间不切换线程，避免了锁的竞争带来的性能损耗

### 2.7 使用了高性能的序列化框架

