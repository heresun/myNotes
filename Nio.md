# Nio
## 传统io和nio的不同
+ 传统io
  + 传统io是面向流的，即通道中传输的是数据流
  + 传统io是单单向的，写入和读取各需要一个流通道
  + 阻塞io
  + 么有选择器
  
+ nio
  + nio是面向缓冲区存取的，通道中传输的是缓冲区，数据在缓冲区中
    
    >如铁路和火车的关系，铁路是通道，火车是缓冲区 
  + nio通道是双向的
  + 非阻塞io（在网络通信中）
  + 有`Selector`选择器（网络通信中）
> Nio的核心是**通道`Channel`**和**缓冲区`Buffer`**,通道负责数据传输，缓冲区负责数据存储

## NIO的缓冲区
缓冲区在nio中负责数据的存取，它说白了就是数组，用于存储不同类型的数据，一般是byte类型的
除boolean外，nio提供了如下类型的缓冲区：

+ ByteBuffer
+ CharBuffer
+ ShortBuffer
+ IntBuffer
+ LongBuffer
+ FloatBuffer
+ DoubleBuffer
> 上述缓冲区都是通过`allocate()`方法获取,除了类型外，操作几乎一致

**以ByteBuffer为例**：
+ 分配缓冲区
  > `ByteBuffer buffer = ByteBuffer.allocate(int size)`,
  > size是缓冲区大小
+ 缓冲的核心属性
  + capacity:容量，缓冲区大小，一旦声明，不可改变
  + limit:界限，表示缓冲区可以操作的数据大小，即limit位置上的数据及后边的数据不可读写
  + position: 位置，表示缓冲区中将要操作数据的位置
  + mark: 标记，可以记录position的位置，可以通过reset(),使position回到mark的位置                                                                                                                            
  > 0<=mark<=position<=limit<=capacity
+ 缓冲区存取数据的两个核心方法
  + put():存储数据到缓冲区
  > buffer.put("sundehui".getBytes());执行后，position为8，limit=capacity=1024，此时还是写的状态
  + get():从缓冲区获取数据
  > ```java
  > //在从缓冲区获取数据之前，一定要调用flip方法，否则乱码
  > //调用完flip之后，position变为0，limit指向原来position的位置，capacity不变
  > buffer.flip();
  > byte[] dst = new byte[buffer.limit()];
  > buffer.get(dst);
  > sout(new String(dst,0,dst.length));
  > 
  > //重新position变为0，limit指向原来position的位置，capacity不变，即重复读原来的数据
  > buffer.rewind();
  > 
  > //清空缓冲区，实际上数据还在，只是重新把position设为0，limit=capacity=1024，原来的数据被“遗忘”了
  > buffer.clear();
  >```
## 非直接缓冲区和直接缓冲区

isDirect()方法判断当前缓冲区对象是直接还是非直接

### 非直接缓冲区
通过allocate方法分配的缓冲区是非直接缓冲区，它将缓冲区建立在JVM的内存中

<img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200318005653057.png" alt="image-20200318005653057" style="zoom: 50%;" />

这种情况下，应用程序无法直接访问物理内存的地址空间

### 直接缓冲区
通过allocateDirect方法分配的缓冲区是直接缓冲区，它将缓冲区建在了物理内存中      

<img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200318005954026.png" alt="image-20200318005954026" style="zoom:50%;" />  

**优缺点**

+ 效率更高
+ 开辟内存消耗更大
+ 不够安全，容易内存泄漏
+ 文件不受应用程序控制，即文件什么时候保存到磁盘上由os说了**算**

**什么时候用？**

建议将其分给易受操作系统io影响的**大型、持久的缓冲区**，一般情况下，仅在直接缓冲区能明显提高性能时才使用。



## 通道 Channel

==通道用于源节点和目标节点的连接，它本身不存数据，所以它需要配合Buffer使用==

### 通道的主要实现类

java.nio.channels.Channel：

+ 文件通道
  + FileChannel: 完成本地文件的传输
+ 网络通道
  + SocketChannel：Tcp
  + ServerSocketChannel：Tcp
  + DatagramChannel：Udp

### 获取通道

==通过getChannel方法获取==

1. 以下类的对象都可以通过getChannel方法获取通道

+ 本地IO
  + FileInputStream
  + FileOutputStream
  + RandomAccessFile
+ 网络IO
  + Socket
  + ServerSocket
  + DatagramSocket

2. 在JDK1.7后对nio改动称为nio2，在nio2中，针对各个通道提供了一个open()方法，可以用它获取通道
3. nio2中的Files工具类的newByteChannel静态方法获取通道

### 通道直接传输数据

```java
FileChannel in = FileChannel.open(
    Paths.get("E:\\java\\dataStru\\src\\main\\resources\\test.txt"), 
    StandardOpenOption.READ);
FileChannel out = FileChannel.open(
    Paths.get("E:\\java\\dataStru\\src\\main\\resources\\test2.txt"),            
    StandardOpenOption.WRITE,
    StandardOpenOption.READ,
    StandardOpenOption.CREATE);

in.transforTo(0,in.size(),out);//将in的数据传输到out中去，从0开始，传输大小为in的大小
out.transforFrom(in,0,in.size());//同上
```



## 分散( Scatter)与聚集( Gather)

+ 分散读取：将通道中的数据分散到多个缓冲区中
+ 聚集写入：将多个缓冲区中的数据聚集到一个通道中

```java
FileChannel in = FileChannel.open(
    Paths.get("E:\\java\\dataStru\\src\\main\\resources\\timg.jpeg"),
    StandardOpenOption.READ);
FileChannel out = FileChannel.open(
    Paths.get("E:\\java\\dataStru\\src\\main\\resources\\timg2.jpeg"),
    StandardOpenOption.WRITE,
    StandardOpenOption.READ,
    StandardOpenOption.CREATE);
//定义两个缓冲区
ByteBuffer allocate1 = ByteBuffer.allocate(100);
ByteBuffer allocate2 = ByteBuffer.allocate(1024);
//将两个缓冲区放到一个数组中
ByteBuffer[] allocates = {allocate1, allocate2};

while (in.read(allocates)!=-1){//分散读
    Arrays.stream(allocates).forEach(item->item.flip());
    out.write(allocates);//聚集写
    Arrays.stream(allocates).forEach(item->item.clear());
}
```



## 字符集 Charset

编码：字符串---->字节数组

解码：字节数组-----> 字符串

反映到程序上主要是ByteBuffer和CharBuffer的转换

```java
//获取字符编码对象
Charset cs = Charset.forName("UTF-8");
ByteBuffer bBuff = cs.encode(CharBuffer);//参数还可以是字符串
CharBuffer cBuff = cs.decode(CharBuffer);
//获取编解码对象
CharsetEncoder encoder = cs.newEncoder();
encoder.encode(CharBuffer);

CharsetDecoder decoder = cs.newDecoder();
decoder.decode(ByteBuffer);
```



## NIO的非阻塞式网络通信

**阻塞**：即如果客户端和服务端建立联系，但是客户端一直未发送数据，**服务端的线程将一直等待**，在此期间什么事情都不做，**也就是说其他客户端的请求都得排队**，等到当前的客户端处理完后再处理排队的。这样一来，服务器的资源利用率就太低了，传统的解决方案是多线程。

**非阻塞**：即在连接没有数据传输时，服务器线程可以去做别的事情

### NIO的非阻塞模型

![image-20200319173035480](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200319173035480.png)

由图可知，客户端和服务器不是直接就可以通信的

客户端要向服务器发送数据，首先和Selector建立通道，选择器检测通道的读、写等状态，当该通道准备就绪时，选择器才将其和服务器相连通，进行数据的交互



### Nio非阻塞网络通信的三要素

+ 通道 （负责链接）

  + java.nio.channels.Channel                       --接口
    + SelectableChannel                             --抽象类
      + SocketChannel                             --用于tcp通信（客户端类）
      + ServerSocketChannel                  --用于tcp通信  ( 服务端类)
      + DatagramChannel                       --用于udp通信
      + Pipe.SinkChannel
      + Pipe.SourceChannel

+ 缓冲区（负责数据存取）

+ 选择器（是SelectableChannel的**多路复用器**，用于监控SelectableChannel的IO状况）

  ![image-20200319182320074](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200319182320074.png)

服务器端代码模板：

```java
//1. 获取服务套接字通道
ServerSocketChannel ssc = ServerSocketChannel.open();
//2. 绑定端口和主机
ssc.socket().bind(new InetSocketAddress("localhost", 8080));
//3. 将服务套接字通道设置为非阻塞
ssc.configureBlocking(false);
//4. 获取选择器对象
Selector selector = Selector.open();
//5. 将通道注册到选择器
ssc.register(selector, SelectionKey.OP_ACCEPT);

while(true) {
    //6. 获取符合选择键类型的就绪通道数量
    int readyNum = selector.select();
    //7. 如果就绪通道数为0，则continue
    if (readyNum == 0) {
        continue;
    }

    //8. 获取SelectKey的迭代器
    Set<SelectionKey> selectedKeys = selector.selectedKeys();
    Iterator<SelectionKey> it = selectedKeys.iterator();

    //9. 对迭代器进行迭代，判断当前的选择键对应的通道是什么状态，并作出相应操作
    while(it.hasNext()) {
        SelectionKey key = it.next();

        if(key.isAcceptable()) {
            // 接受连接
            SocketChannel cChannel = ssc.accept();
            // 将获取到的客户端通道设置为异步的
            cChannel.configureBlocking(false);
            //将客户端通道注册到选择器，并指定选择键，明确其监听哪一种事件
            cChannel.register(selector,SelectionKey.OP_READ);
        } else if (key.isReadable()) {
            // 通道可读
            //获取可读的客户端通道
            SocketChannel cChannel = key.channel();
            ...;
            //关闭通道
            cChannel.close();
                
        } else if (key.isWritable()) {
            // 通道可写
        }

        it.remove();
    }
}

ssc.close();
```

客户端代码模板

```java
//1. 创建通道
SocketChannel sChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1",8899));

//2. 将通道切换为非阻塞状态
sChannel.configureBlocking(false);	
...;
//3. 关闭通道
sChannel.close();

```

## Pip 线程间通信

==线程间必须共用一个pipe对象==

```java
//获取管道对象
Pipe pipe = Pipe.open();
//================================线程一=====================================
//1-1 从管道对象中获取SinkChannel对象
Pipe.SinkChannel sinkChannel = pipe.sink();
//1-2 向pipe的入口写入数据
sinkChannel.write(ByteBuffer);
//================================线程二====================================
//2-1 从管道对象中获取SourceChannel对象
Pipe.SourceChannel sourceChannel = pipe.source();
//2-2 从pipe的出口读取数据
sourceChannel.read(ByteBuffer);

```

