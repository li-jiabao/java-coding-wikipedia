# Java的IO

## 1 BIO

阻塞式IO，当服务端等待客户连接的时候，线程是阻塞的，当客户端读取数据的时候，读取操作也是阻塞的，只有当有数据读取的时候才会继续进行。

```java
package com.jiabao.netty;

import java.io.IOException;
import java.io.InputStream;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

/**
 * 基于线程池机制实现的BIOserver
 * 这一类IO通信方式的缺点就是需要维护线程，同时一个客户端一个线程的方式对于资源消耗很大
 * 线程维护也需要耗费资源，当一个连接进来并没有做什么事也需要维护这一个线程，开销很大
 * 适合场景：
 *      用户连接量小，计算机资源丰富的场景，适合并发量小的应用
 */
public class BIOServer {
    public static void main(String[] args) throws IOException{
        ServerSocket server = new ServerSocket(8888);

        // 创建一个自动扩容的线程池，但是这种线程池阿里不推荐，因为可能会出现OOM问题
        ExecutorService cachedThreadPool = Executors.newCachedThreadPool();

        while (true) {
            // 正在等待客户端连接
            System.out.println("正在等待客户端连接");
            Socket client = server.accept();
            System.out.println("已经有一个客户端连接进来了");
            System.out.println("当前线程名：" + Thread.currentThread().getName()+ "\t线程ID："+Thread.currentThread().getId());
            cachedThreadPool.execute(new Runnable() {
                @Override
                public void run() {
                    System.out.println("当前线程名：" + Thread.currentThread().getName()+ "\t线程ID："+Thread.currentThread().getId());
                    handler(client);
                }
            });
        }
    }

    private static void handler(Socket client) {
        byte[] datas = new byte[1024];
        while (true) {
            InputStream inputStream = null;
            try {
                inputStream = client.getInputStream();
            } catch (IOException e) {
                e.printStackTrace();
            }
            System.out.println("正准备读取数据");
            // 读取客户端输入流的数据，传统的BIO会在这里被阻塞，当有数据时才会继续向下执行
            int read = -1;
            try {
                read = inputStream.read(datas);
            } catch (IOException e) {
                e.printStackTrace();
            }
            if (read!=-1) {
                System.out.println(new String(datas,0,read));
            }else {
                System.out.println("流读取结束了，正在退出");
                try {
                    client.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                break;
            }
        }
    }
}

```



## 2 NIO

非阻塞式IO，Java为了提高IO效率，减少BIO的资源浪费

### 2.1 NIO组件

NIO的大体流程：

1. 将channel对象注册到Selector中
2. 条用Selector的select方法，这个方法阻塞
3. 当某个注册到Selector中的channel有TCP连接或者可读写事件，channel就会就绪，Selector可以将这个channel通过轮询的方式找出
4. 通过SelectionKey获取就绪的channel集合，用来进行后续的IO操作

#### 2.1.1 Selector

Selector是用来监视多个channel对象，channel中数据到达、连接打开时，就会进行对应事件发生的channel中数据的读写处理。因此只需要一个Selector就可以监视多个channel，一个Selector就对应一个线程

channel和Selector之间通过注册来进行通信，当channel对象注册到Selector时，返回一个SelectionKey 对象（表示一个特定的通道对象和一个特定的选择器对象之间的注册关系）。使用SelectionKey对象可以了解channel中哪些IO事件已经就绪。通过这个SelectionKey对象可以获取channel操作这些IO数据

```java
public abstract class Selector {
    
    // 用来检测当前的channel集合中是否有集合有事件需要处理，返回值是有事件的channel数量
    public abstract int select() throws IOException;
    
    // 有超时设置的select方法，阻塞这么久没事件需要返回
    public abstract int select(long timeout) throws IOException;
    
    // 不阻塞，立马返回，查看是否有事件
    public abstract int selectNow() throws IOException;
    
    // 唤醒Selector
    public abstract Selector wakeup();
}
```

一个Selector的流程代码：

```java
package com.jiabao.netty.nio;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.net.Socket;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.Iterator;
import java.util.Set;

/**
SelectionKey.OP_READ ：读事件
SelectionKey.OP_ACCEPT ：服务端接收到连接事件
SelectionKey.OP_WRITE :写事件
SelectionKey.OP_CONNECT : 连接事件
*/
public class NIOSelector {
    public static void main(String[] args) throws IOException {
        // 创建一个服务器ServerSocketChannel
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        // 创建一个选择器
        Selector selector = Selector.open();
        // 服务端绑定端口8888
        serverSocketChannel.bind(new InetSocketAddress(8888));
        // 设置channel为非阻塞
        serverSocketChannel.configureBlocking(false);
        // 将ServerSocketChannel注册到Selector上,并指定监测事件
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);

        System.out.println("服务器正在监听8888端口");

        while (true) {
            if (selector.select(1000)==0) {
//                System.out.println("服务端1s，无客户端连接");
                continue;
            }

            // 获取当前有事件的SelectionKey集合
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            // 构建selectionKeys集合迭代器
            Iterator<SelectionKey> keys = selectionKeys.iterator();

            while (keys.hasNext()) {
                // 获取一个有事件的SelectionKey
                SelectionKey selectionKey = keys.next();
                // 判断事件是否有连接
                if (selectionKey.isAcceptable()) {
                    System.out.println("有一个客户端连接");
                    SocketChannel socketChannel = serverSocketChannel.accept();
                    socketChannel.configureBlocking(false);
                    socketChannel.register(selector,SelectionKey.OP_READ);
                    System.out.println(socketChannel.getRemoteAddress().toString().substring(1)+"上线了");
                }
                // 判断当前事件是否可读取
                else if (selectionKey.isReadable()) {
                    System.out.println("有一个客户端数据可读取");
                    handleRead(selectionKey);
                }
                // 需要手动从集合中去除当前的SelectionKey,防止后续重复操作
                keys.remove();
            }
        }
    }

    public static void handleRead(SelectionKey key) throws IOException {
        // 获取有事件的SelectionKey对应的channel
        SocketChannel channel = (SocketChannel) key.channel();
        ByteBuffer byteBuffer = ByteBuffer.allocate(5);
        int read;
        while ((read=channel.read(byteBuffer))>0) {
            byteBuffer.flip();
            System.out.println(new String(byteBuffer.array(),0,read));
            byteBuffer.clear();
        }
        if (read==0) {
            System.out.println("没有数据可以读取了");
        }else {
            System.out.print("客户端没有数据传输了\t");
            System.out.print(channel.getRemoteAddress().toString().substring(1));
            System.out.print("断开连接了\n");
            key.cancel();
        }
    }
}
```



#### 2.1.2 Buffer

NIO中的所有数据读写都是面向缓冲区来进行的

```java
/**
* buffer的源码，其他的Buffer类都是继承这个类来实现的
*/

public abstract class Buffer {
    // Buffer对象中重要的一些成员变量
	private int mark = -1;               // 一个标志位，reset方法需要使用到这个标志位
    private int position = 0;            // 记录当前操作的元素所在的索引位置
    private int limit;                   // 限制位，限制读写的最大索引位置
    private int capacity;                // Buffer的容量大小
    
    // 清除buffer中的内容，将指针移动到开头，最大索引位置设置位容量
    public Buffer clear() {
        position = 0;
        limit = capacity;
        mark = -1;
        return this;
    }
    
    // 重置Buffer到mark所在位置
    public Buffer reset() {
        int m = mark;
        if (m < 0)
            throw new InvalidMarkException();
        position = m;
        return this;
    }
    
    // 将当前buffer对象的位置移动到指定位置
    public Buffer position(int newPosition) {
        if (newPosition > limit | newPosition < 0)
            throw createPositionException(newPosition);
        if (mark > newPosition) mark = -1;
        position = newPosition;
        return this;
    }
    
    // 反转位置，将limit设置位容量，pos设置位初始位置
    public Buffer flip() {
        limit = position;
        position = 0;
        mark = -1;
        return this;
    }
    
    // 查看还有多少个可读写数据
    public final int remaining() {
        int rem = limit - position;
        return rem > 0 ? rem : 0;
    }
    
    // 判断是否还有可读写数据
	public final boolean hasRemaining() {
        return position < limit;
    }
}
```

Buffer子类：（可以使用asReadOnlybuffer变为只读buffer）对这个buffer写操作会报错`ReadOnlyBufferException`

- ByteBuffer
- ShortBuffer
- IntBuffer
- LongBuffer
- FloatBuffer
- DoubleBuffer
- CharBuffer

MappedBuffer：映射Buffer，可以直接在内存（Java堆外内存）中进行操作，不用再多进行一次复制，操作系统不用再拷贝一次。使用channel的map方法创建一个MappedBuffer。

buffer的分散和聚合：

- 多个缓冲区的同时操作，采用buffer数组依次进行操作即可

```java
     public void bufferScatteringAndGathering() throws IOException {
          ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
          InetSocketAddress address = new InetSocketAddress(8888);
          serverSocketChannel.bind(address);

          ByteBuffer[] buffers = new ByteBuffer[2];
          buffers[0]=ByteBuffer.allocate(5);
          buffers[1]=ByteBuffer.allocate(3);

          SocketChannel socketChannel = serverSocketChannel.accept();

          while (true) {
               int read = (int) socketChannel.read(buffers);
               if (read==-1) break;
               System.out.println("从客户端读取了"+read+"个字节");
               // Java8的流机制
               Arrays.asList(buffers).stream().
                       map(buffer -> "position"+buffer.position()+" limit" + buffer.limit()).
                       forEach(System.out::println);
               Arrays.asList(buffers).stream().
                       map(buffer->new String(buffer.array(),0,buffer.position())).
                       forEach(System.out::println);
               // 每次对缓冲区读写之后都需要清理缓冲区，不然会报错
               Arrays.asList(buffers).stream().forEach(buffer->buffer.clear());
          }
     }
```



#### 2.1.3 Channel

通道类似于流，但是有一些区别，channel需要搭配buffer一起使用：

- 通道可以**同时进行读写**，流只可以读或者写
- 通道可以实现**异步读写**数据
- 通道可以从缓冲区读数据，也可以写数据到缓冲区

```java
// channel是一个接口，实现了Closeable接口
public interface Channel extends Closeable {
    
    public boolean isOpen();
    
	public void close() throws IOException;
}
```

channel的常用的实现类：(Channel类中有一个register方法，将这个通道注册到Selector上，返回一个SelectionKey，通过SelectionKey的channel方法获取有事件的channel)

- FileChannel：用于文件的读写 
- DatagramChannel：用于UDP数据的读写
- ServerSocketChannel：用于TCP服务端的数据读写
- SocketChannel：用于TCP数据的读写

