# IO多路复用

IO复用：进程告知内核，如果IO条件就绪就通知进程，这样的一个能力被称之为 IO复用

IO的多路复用：

- 单线程或者单进程同时检测若干个文件描述符是否可以执行IO操作的能力
- 当其中一个文件描述可用，就可以返回执行

多路复用技术可以避免同步非阻塞的轮询等待的消耗。当socket文件准备就绪时，内核会通知线程准备就绪可以读取了，然后就进行数据的读取，然后返回。

IO多路复用，事件循环将文件句柄的状态通知给用户线程，然后由用户线程自己区读取数据

![image-20211019212459475](https://gitee.com/Jia_bao_Li/img/raw/master/img/IO%E5%A4%9A%E8%B7%AF%E5%A4%8D%E7%94%A8%E7%9A%84%E5%8E%9F%E7%90%86.png)

## SELECT实现

指示内核等待多个事件中的任何一个发生，并只在一个或多个事件发生或者经历指定的时间之后唤醒进程

调用select指定描述符集合，当集合中的某个描述符就绪，内核就会通知。

问题：

- 描述符限制1024，超过了这个数目不能使用select

## POLL实现

执行和select类似的任务，等待一些列文件描述中的某一个IO就绪。存储的结构是struct对象数组

poll没有select的文件描述符限制

## EPOLL实现

类似于poll系统调用，监视多个关注的文件描述符查看其中是否有IO准备就绪。

触发方式有两种：

- 水平触发level-triggered：和poll有相同的语义
- 边缘触发edge-triggered

epoll实例从用户空间角度看是一个在内核内部的数据结构，是两个列表的容器

- interest List ：epoll感兴趣的文件描述的集合
- ready list：IO就绪的文件描述符列表

epoll相关的几个系统调用：

- `epoll_create`：创建一个epoll实例，并返回一个指向当前实例的文件描述符
- `epoll_wait`：等待IO时间，如果当前没有事件，当前的线程会被阻塞，有就从就绪列表中取出项目
- `epoll_ctl(int epfd,int op,int fd,struct epollevent *event)`：用来网interest列表中增加修改删除文件描述符的函数.epfd是epoll实例，op指定对epfd执行什么操作（增、删、改），fd，增删改操作对应的文件描述符



红黑树+链表的实现，节省了空间，优化了查找有IO事件的文件描述符的速度。

只需要将有事件的文件描述符放到一个链表中复制到用户态进行处理，节省了不少空间，此外也提高了处理事件的响应速度，提高了吞吐量

epoll是可以解决C10K问题的



## 异步IO

异步IO模型中，当用户线程收到通知时，内核已经将数据读取完毕并放在了用户线程指定的缓冲区，内核通知线程直接去使用数据即可

## Java的IO模型

### BIO阻塞式的多线程IO

就是对于每一个连接，都为这个连接创建一个线程用来监听对应的操作

```java
ServerSocket ss = new ServerSocket(9090);     // 创建一个Socket服务端监听9090端口

while (true) {
    Socket client = ss.accept();             // 对于每一个client的管理采用创建线程的方式来支持并发
}
```

```c
// linux的底层系统调用，Java BIo代码执行过程中的
socket() = 4;		 // 创建socket
bind(3,9090);		 // 绑定端口
listen(4);           // 监听
accept(4);           // 接收连接
clone();             // 创建线程
```

缺点：

- 底层需要频繁的系统调用
- 且针对每一个连接都要创建线程管理，消耗大
- 对于每一个系统调用都会阻塞等待

### NIO非阻塞式IO

![image-20211028164918105](https://gitee.com/Jia_bao_Li/img/raw/master/img/NIO%E7%BB%84%E4%BB%B6.png)

- 一个selector对应一个线程
- 一个selector可以绑定多个channel
- 每个channel都有各自的buffer缓冲区
- **buffer缓冲区**实际上就是内存块，底层数据结构是数组
- buffer是双向的，可以读可以写
  - Java的buffer的抽象类：ByteBuffer、CharBuffer、IntBuffer、LongBuffer、ShortBuffer、FloatBuffer和DoubleBuffer
- channel也是双向的，可以传入可以传出
  - FileChannel：用来文件读写
  - DatagramChannel：用于UDP数据的收发
  - ServerSocketChannel：用来TCP的数据手法
  - SocketChannel：用于客户端的TCP数据包的手法
- selector会根据具体的事件在不同的buffe上切换（事件驱动）

下面的图展示了IO的三种读写技术：

- 直接IO技术，IO在内核空间和用户空间传递
- 内存映射文件技术，将内核缓冲区直接映射成一个进程的文件
- 零拷贝技术：将内核缓冲区和Socket缓冲区也进行映射

减少了IO复制的次数，提升效率

![image-20211028170421815](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E7%9B%B4%E6%8E%A5IO.png)

![image-20211028170441549](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E5%86%85%E5%AD%98%E6%98%A0%E5%B0%84%E6%96%87%E4%BB%B6.png)

![image-20211028170504470](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E9%9B%B6%E6%8B%B7%E8%B4%9D%E6%8A%80%E6%9C%AF.png)

```java
// Java非阻塞的实现
List<SocketChannel> list = new ArrayList<>();
ServerSocket server = ServerSocketChannel.open();       // 服务端开启建通
server.bind(new InetSocketAddress(9090));               // 绑定监听端口
server.configureBlocking(false);                       // 配置非阻塞，和操作系统的非阻塞相关
while (true) {
    SocketChannel client = server.accpt();             // 接收客户端连接对象,这里代码不会阻塞
    if (client!=null) {
        client.configureBlocking(false);               // 将客户端的连接也设置为非阻塞
        int port = client.socket().getPort();
        list.add(client);
    }
}
```

```c
// Linux下的Java执行NIO的系统调用
// linux的底层系统调用，Java BIo代码执行过程中的
socket() = 4;		 // 创建socket
bind(4,9090);		 // 绑定端口
listen(4);           // 监听
fcntl(4,NON_BLOCKING); // 设置为非阻塞，底层操作系统的实现
accept(4) = 5;           // 接收连接
// 当返回值不为-1说明有连接
fcntl(5,NON_BLOCKING);    // 客户端页需要设置非阻塞
```

弊端：

- 连接数量过大时对资源消耗还是很严重
- 且如果这些连接中只有少数连接有数据，浪费了很多不必要的资源

### 多路复用器的IO方式

Java的多路复用器只有selector API，是对select、poll和epoll的一个封装

#### select：

- 需要重复传参`fd_set`
- 文件描述符数量有限制

```c
socket() = 4;		 // 创建socket
bind(4,9090);		 // 绑定端口
listen(4);           // 监听
while (true) {
    select();        // 监听连接中是否有就绪事件,返回就绪的文件描述符
    accept();
    recv();
}
```

#### epoll：

```c
// epoll的基本流程简示
socket();
bind();
listen;

epoll_create()=8;              // 创建epoll实例
epoll_ctl(8,add,4,accept);     // 向epoll实例加入监听的文件描述符和监听事件
epoll_wait();                  // 等待监听的文件描述中的事件就绪
accept(4) = 7;
epoll_ctl(8,add,7,recv);       // 等待7的可读取
```

JAVA 的NIO使用selector实现的服务器的代码：

```java
// Java的NIO包中都有相关的
public static void main(String[] args) throws IOException {
    ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
    Selector selector = Selector.open();

    // 绑定端口
    serverSocketChannel.socket().bind(new InetSocketAddress(8080));

    // 设置 serverSocketChannel 为非阻塞模式
    serverSocketChannel.configureBlocking(false);

    // 注册 serverSocketChannel 到 selector，关注 OP_ACCEPT 事件
    serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

    while (true) {
        // 没有事件发生
        if (selector.select(1000) == 0) {
            continue;
        }

        // 有事件发生，找到发生事件的 Channel 对应的 SelectionKey 的集合
        Set<SelectionKey> selectionKeys = selector.selectedKeys();

        Iterator<SelectionKey> iterator = selectionKeys.iterator();
        while (iterator.hasNext()) {
            SelectionKey selectionKey = iterator.next();

            // 发生 OP_ACCEPT 事件，处理连接请求
            if (selectionKey.isAcceptable()) {
                SocketChannel socketChannel = serverSocketChannel.accept();

                // 将 socketChannel 也注册到 selector，关注 OP_READ
                // 事件，并给 socketChannel 关联 Buffer
                socketChannel.register(selector, SelectionKey.OP_READ, ByteBuffer.allocate(1024));
            }

            // 发生 OP_READ 事件，读客户端数据
            if (selectionKey.isReadable()) {
                SocketChannel channel = (SocketChannel) selectionKey.channel();
                ByteBuffer buffer = (ByteBuffer) selectionKey.attachment();
                channel.read(buffer);

                System.out.println("msg form client: " + new String(buffer.array()));
            }

            // 手动从集合中移除当前的 selectionKey，防止重复处理事件
            iterator.remove();
        }
    }
}
```

## 几种Ractor线程模式

### Ractor的基本形态

![image-20211028172532784](https://gitee.com/Jia_bao_Li/img/raw/master/img/Racttor%E5%8D%95%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%BC%8F.png)

### Ractor单线程模式

![image-20211028172715063](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E5%8D%95%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%BC%8F.png)

### Ractor多线程模式

![image-20211028172737672](https://gitee.com/Jia_bao_Li/img/raw/master/img/Ractor%E5%A4%9A%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%BC%8F.png)

### Ractor主从多线程模式

![image-20211028172804766](https://gitee.com/Jia_bao_Li/img/raw/master/img/%E4%B8%BB%E4%BB%8ERactor%E5%A4%9A%E7%BA%BF%E7%A8%8B%E6%A8%A1%E5%BC%8F.png)

## Netty框架

异步高并发的网络组件，常用于构建高性能的RPC框架，提升分布式微服务群之间调用或者数据传输的并发度和速度

## 

![image-20211028160348445](https://gitee.com/Jia_bao_Li/img/raw/master/img/Netty%E6%A1%86%E6%9E%B6%E6%9E%B6%E6%9E%84%E5%9B%BE.png)

