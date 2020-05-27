

# 1.将tomcat导入idea

### 1.1 下载tomcat源码

https://tomcat.apache.org/download-80.cgi

### 1.2 用idea创建一个空项目

### 1.3 解压缩，并将解压缩后的文件放入空项目中，进入文件中创建home文件夹，并将webapps和conf文件夹拷入home

### 1.4 创建pom.xml文件，内容如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
 
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>Tomcat8.5</artifactId>
    <name>Tomcat8.5</name>
    <version>8.5</version>
 
    <build>
        <finalName>Tomcat8.5</finalName>
        <sourceDirectory>java</sourceDirectory>
        <testSourceDirectory>test</testSourceDirectory>
        <resources>
            <resource>
                <directory>java</directory>
            </resource>
        </resources>
        <testResources>
           <testResource>
                <directory>test</directory>
           </testResource>
        </testResources>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
 
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.easymock</groupId>
            <artifactId>easymock</artifactId>
            <version>3.4</version>
        </dependency>
        <dependency>
            <groupId>ant</groupId>
            <artifactId>ant</artifactId>
            <version>1.7.0</version>
        </dependency>
        <dependency>
            <groupId>wsdl4j</groupId>
            <artifactId>wsdl4j</artifactId>
            <version>1.6.2</version>
        </dependency>
        <dependency>
            <groupId>javax.xml</groupId>
            <artifactId>jaxrpc</artifactId>
            <version>1.1</version>
        </dependency>
        <dependency>
            <groupId>org.eclipse.jdt.core.compiler</groupId>
            <artifactId>ecj</artifactId>
            <version>4.5.1</version>
        </dependency>
       
    </dependencies>
</project>
```



### 1.5 导入idea

注意：创建新空项目后，右侧maven按钮消失，点击上方放大镜，搜索maven即可

选择导入maven工程，选中pom.xml文件，确认

### 1.6 选择主类

![image-20200520002216857](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520002216857.png)

### 1.7 虚拟机设置

```properties
-Dcatalina.home=E:/java/tomcat/apache-tomcat-8.5.55-src/home
-Dcatalina.base=E:/java/tomcat/apache-tomcat-8.5.55-src/home
-Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager
-Djava.util.logging.config.file=E:/java/tomcat/apache-tomcat-8.5.55-src/home/conf/logging.properties
```

### 1.8 启动

在启动过程中，报错`test.util.TestCookieFilter`，将该类注释即可

### 1.9 访问"localhost:8080"

![image-20200520004952218](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520004952218.png)

原因是无法解析jsp

解决方法

在`org.apache.catalina.startup.ContextConfig`类的**configureStart**方法的**webConfig();**代码后添加如下

```java
context.addServletContainerInitializer(new JasperInitializer(),null);
```

==到此为止，导入idea成功==

![image-20200520005518057](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520005518057.png)



# 2 tomcat架构

### 2.1 http的工作原理

HTTP是应用层协议，基于TCP/IP协议传输数据。

HTTP协议不涉及数据包的传输，它主要规定了客户端和服务器的通信格式。

### 2.2 tomcat整体架构

#### 2.2.1 http服务请求处理

浏览器发送给服务器一个http请求，服务器接受后调用服务端程序处理，所谓服务端程序，即由程序员写的Java类，一般来说不同的请求需要不同的类处理。

![image-20200520010546477](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520010546477.png)

+ 图1，http服务器直接调用具体的业务类，它们是之间紧耦合的

+ 图2，http服务器将请求交给servlet容器处理，容器通过servlet接口调用业务类。这样，通过servlet容器和servlet接口达到了http服务器和业务类解耦的目的。servlet容器和servlet接口叫做servlet规范。

  tomcat按照servlet规范要求实现了servlet容器，同时它们也具有http服务器的功能。作为Java程序员，要实现新的业务功能，只需要实现一个servlet，并把它注入到tomcat (servlet) 容器中，剩下的事情tomcat就帮我们处理了。

#### 2.2.2 servlet容器工作流程

为了解耦，http服务器不直接调用servlet，而是把请求交给servlet容器，servlet容器又是怎么工作的呢？

1. 当客户请求某一资源时
2. http服务器会用一个ServletRequest对象把客户的请求信息封装起来
3. http服务器调用Servlet容器的service方法，即把请求交给servlet容器
4. servlet容器拿到请求后，根据url和servlet的映射关系，找到响应的servlet，如果servlet还未被加载，用反射创建该servlet，并且调用servlet的init方法完成初始化
5. 紧接着调用servlet的service方法处理请求
6. 把ServletResponse对象返回给http服务器，http服务器再将其返回给客户端。

![image-20200520012151747](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520012151747.png)

#### 2.2.3 tomcat整体架构

tomcat的两个核心功能：

1. 处理socket连接，负责网络字节流与request和response对象之间的转化
2. 加载和管理servlet，以及具体处理request请求

因此，tomcat设计了两个核心组件：连接器（Connector）和容器（Container）来做这两件事情。

连接器负责对外交流，容器负责内部处理

![image-20200520012840365](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520012840365.png)

### 2.3 连接器-Coyote

#### 2.3.1 架构介绍

Coyote是tomcat连接器名称，是tomcat服务器提供的供客户端访问的外部接口，客户端通过Coyote与服务器建立连接、发送请求和响应。

Coyote封装了底层的网络通信（Socket 请求和响应），为Catalina容器提供了统一的接口，使Catalina容器与具体的请求协议和IO操作方式完全解耦。Coyote将Socket输入封装为**Request**对象，交友Catalina容器处理，处理完成后，Catalina通过Coyote提供的**Response**对象将结果写入输出流。

Coyote作为独立的模块，只负责具体协议和IO操作，与servlet规范的实现没有直接关系，因此即便是**Request**和**Response**对象也并未实现servlet规范对应的接口，而是再Catalina中将他们进一步封装为**ServletRequest**和**ServletResponse**对象。

![image-20200520013844536](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520013844536.png)

#### 2.3.2 IO模型与协议

Coyote支持多种IO模型与协议，如下表：

| IO模型 | 描述                                                         |
| ------ | ------------------------------------------------------------ |
| NIO    | 非阻塞IO，使用java NIO类库实现                               |
| NIO2   | 异步IO，使用jdk 7最新NIO2类库实现                            |
| APR    | 采用apache可移植库实现，是c/c++实现。如果选择该方案，需要单独安装APR库 |

<span style="font-size:12px;color:red">注：自8.5后，tomcat不支持BIO</span>

| 应用层协议 | 描述                                                         |
| ---------- | ------------------------------------------------------------ |
| HTTP/1.1   | 大部分web采用的协议                                          |
| AJP        | 用于和web服务器集成，以实现对静态资源优化以及集群部署，当前支持AJP1.3 |
| HTTP/2     | 大幅度提高了web性能，自8.5以后支持                           |

协议分层：

![image-20200520015229311](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520015229311.png)

Tomcat为了实现支持多种IO模型和应用层协议，允许一个容器可以对接多个连接器，好比一个房间有多个门。但是单独的连接器或者容器不能提供服务，必须二者组合，二者组合的整体叫做Service组件。

注意：Service本身没有做什么重要的事，只是连接器和容器的一个壳子，负责将它们组装在一起。Tomcat内部可能有多个Service，这样设计是出于灵活的考虑。通过在tomcat中配置多个Service，可以实现通过不同的端口号来访问同一台机器上部署的不同应用。

#### 2.3.3 连接器组件

![image-20200520020018324](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520020018324.png)

连接器中各个组件的作用如下：

**EndPoint**

1. EndPoint：Coyote的通信端点，即通信监听的端口，是具体Socket接受和发送处理器，是对传输层的抽象。因此，EndPoint是用来实现TCP/IP协议的。
2. Tomcat并没有EndPoint接口，而是提供了一个抽象类AbstractEndPoint，其中定义了两个内部类，Acceptor和SocketProcessor。Acceptor用来监听Socket连接请求，SocketProcessor用来处理接收到的Socket请求，它实现了Runnable接口，在run方法中调用协议处理组件Processor进行处理。为了提高处理能力，SocketProcessor被提交到线程池来执行，这个线程池叫做执行器（Executor）。

**Processor**

Processor是Coyote的协议处理接口，如果说EndPoint是用来实现TCP/IP协议的，那么Processor就是用来实现HTTP协议的。Processor接收来自EndPoint的Socket，读取字节流，解析成Tomcat Request和Response对象，并通过Adapter将其提交到容器处理，Processor是对应用层协议的抽象。

**ProtocolHandler**

Coyote的协议接口，通过EndPoint和Processor实现对具体协议的处理能力。Tomcat按照协议和IO提供了6个实现类：AjpNioProtocol、AjpAprProtocol、AjpNio2Protocol和Http11NioProtocol、Http11AprProtocol、Http11Nio2Protocol。在配置server.xml时，至少要指定具体的ProtocolHandler，也可以指定协议名，如：HTTP/1.1, 如果安装了APR，那么将使用Http11AprProctol，否则使用Http11NioProtocol

**Adapter**

由于协议的不同，客户端发来的请求信息也不尽相同，Tomcat定义了自己的Request类来存放这些请求信息。

ProtocolHandler接口负责解析请求并生成Tomcat Request类，但是这个Request类不是标准的ServletRequest，意味着不能使用Tomcat Request作为参数调用容器。因此引入CoyoteAdapter（适配器模式），连接器调用CoyoteAdapter的service方法，传入的Tomcat Request对象，CoyoteAdapter负责将Tomcat Request转换为ServletRequest，再调用容器的service方法。

### 2.4 容器 - Catalina

tomcat是由一系列可以配置的组件构成的web容器，Catalina是tomcat的servlet容器

Catalina是实现了servlet规范的servlet容器，涉及到后续的安全、会话、集群、管理等servlet容器架构的各个方面。它通过松耦合的方式集成Coyote，以完成按照请求协议进行数据读写。同时它还包括我们的启动入口、shell等。

#### 2.4.1 Catalina 的地位

tomcat的模块分层结构图：

![image-20200520170127174](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520170127174.png)

Catalina是tomcat的核心，其他模块都是为其提供功能支撑的。如：Coyote模块提供连接通信，Jasper模块提供Jsp引擎，Naming提供JNDI（Java Naming and Directory Interface）服务，Juli提供日志服务。

#### 2.4.2 Catalina结构

![image-20200520170804386](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520170804386.png)

如上图所示，Catalina负责管理Server，而Server表示整个服务器。一个Server下有多个服务Service，每个服务都包含着多个连接组件Connector（Coyote实现）和一个容器组件Container，在tomcat启动时，会初始化一个Catalina实例。

Catalina各个组件的职责:

| 组件      | 职责                                                         |
| --------- | ------------------------------------------------------------ |
| Catalina  | 负责解析tomcat配置文件，以此来创建服务器Server组件，并根据命令对其管理 |
| Server    | 服务器表示整个Catalina Servlet容器和其他组件，负责组装并启动servlet容器引擎，tomcat连接器。Server通过实现Lifecycle接口，提供了一种优雅的启动和关闭整个系统的方式。 |
| Service   | Server内部的一个组件，一个Server包含多个Service。Service将若干个Connector组件绑定到一个Container（Engine）上。 |
| Connector | 连接器，处理客户端与服务器通信                               |
| Container | 容器，负责处理客户端请求，并返回对象给客户端的模块           |

Service、Container和Connector在代码中的反映：

![image-20200520233718803](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520233718803.png) 

#### 2.4.3 Container结构

tomcat设计了四种容器，分别是Engine、Host、Context、Wrapper。这四种容器是父子关系，tomcat通过分层架构，让Servlet容器具有很好的灵活性。

![image-20200520234027300](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200520234027300.png)

各个组件的含义：

| 容器    | 描述                                                         |
| ------- | ------------------------------------------------------------ |
| Engine  | Container的Servlet引擎，用来管理多个虚拟站点，一个Service最多有一个Engine，一个Engine可以包含多个Host |
| Host    | 代表一个虚拟主机，或者说一个站点，可以给tomcat分配多个虚拟主机，一个虚拟主机下可以包含多个Context |
| Context | 一个Web应用程序，一个Web应用程序可以包含多个Wrapper          |
| Wrapper | 一个Servlet，它作为容器中的最底层，不能有子容器              |

tomcat的配置文件server.xml反映了上述这种结构，由外到内层的是

Server<Service，<Connector,Engine\<Host\<Context>>>>，最外层的是Server，其他组件按照一定格式要求配置在这个顶层容器中。

```xml
<Server>
    <Connector></Connector>
    <Connector></Connector>
    <Engine>
    	<Host>
            <Context>
                <Wrapper></Wrapper>
            </Context>
        </Host>
    </Engine>
</Server>
```

tomcat是如何管理这些组件的呢？由前文只，这些容器有父子关系，形成一个树状结构，所以，tomcat是使用组合模式管理这些组件。具体方法为：所有容器都实现了Container接口，一次组合模式可以使得用户对单容器对象和组合容器对象的使用具有一致性。这里但容器对象是指底层的Wrapper，组合容器对象是指Context，Host，Engine

![image-20200521000426721](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200521000426721.png)

Container接口中提供了以下方法（部分）：

![image-20200521000658801](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200521000658801.png) 

### 2.5 Tomcat启动流程

#### 2.5.1 启动流程

![image-20200521000935925](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200521000935925.png)

步骤：

1. 调用 bin/startup.bat（startup.sh），在 startup.bat 脚本中调用 catalina.bat
2. 在 catalina.bat 中调用了 BootStrap 的 main 方法
3. 在 BootStrap 的 main 方法中调用了 ini t方法，来创建 Catalina 和初始化类加载器
4. 在 BootStrap 的 main 方法中调用 load 方法，在其中又调用了 Catalina 的 load 方法
5. 在 Catalina 的 load 方法中需要进行一些初始化工作，并需要构建 Digester 对象用以解析XML
6. 然后调用后续组件初始化操作

总之：加载tomcat的配置文件，初始化容器组件，监听对应端口号，准备接受客户端请求

#### 2.5.2 源码解析

##### 2.5.2.1 Lifecycle

从上面 tomcat 开启流程图可知，tomcat的组件都有一些共同的方法，如 init、load、start等，所以在 tomcat 设计时，基于生命周期管理抽象成了一个接口：**Lifecycle**，而组件 Server、Service、Container、Executor、Connector 组件，都实现了生命周期接口，从而具有以下生命周期中的核心方法：

+ init()
+ start()
+ stop()
+ destroy()

![image-20200521003211739](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200521003211739.png)

##### 2.5.2.2 各组件的默认实现

上文提到 Server、Service、Engine、Host、Context 都是接口，下图罗列了这些接口的默认实现类。EndPoint 组件在tomcat中没有 EndPoint 接口，对应的是一个AbstractEndPoint抽象类。

![image-20200521003915908](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200521003915908.png)



ProtocolHandler ：Coyote的协议接口，通过封装EndPoint和Processor，拥有针对具体协议的处理功能。tomcat按照协议和IO提供了6个实现类：

![image-20200521004345836](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200521004345836.png)

##### 2.5.2.3 总结

从tomcat启动流程图和源码中，可以看到tomcat的启动过程十分标准，统一按照生命周期管理接口Lifecycle的定义进行启动。首先调用 init() 方法进行组件逐级初始化，再调用 start() 方法进行启动。

每一级的组件除了完成自身的处理外，还要负责调用子组件响应的生命周期管理方法，组件与组件之间是松耦合的，可以很容易通过配置文件进行修改和配置。

### 2.6 tomcat 的请求处理流程

设计了这么多层的容器，tomcat是怎么确定某个请求是由哪个 Wrapper 来处理的呢？答案是 **Mapper** 组件来完成这个任务。

Mapper 组件的功能就是将用户的url请求定位到一个Servlet。它的工作原理：Mapper 组件中保存了web应用的配置信息，其实就是容器组件（Server -> Service -> Engine -> Host -> Context -> Wrapper）与访问路径的映射关系，比如 Host 容器中配置的域名，Context 容器中的 web应用路径，Wrapper容器中的Servlet映射路径。所以可以将这些配置信息看作一个多层次的 Map。

当一个请求到来：Mapper 组件通过解析 url 请求的域名和路径，再到自己保存的Map中查找，就能定位一个Servlet。注意：一个url请求最后只会定位到一个Wrapper容器，即一个Servlet。

下图描述了，url为 `http://www.itcast.cn/bbs/findAll` 的请求是如何找到最终处理业务逻辑的Servlet的：

![image-20200521020538921](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200521020538921.png)

上图仅仅从url请求的角度描述了如何找到处理相应请求的Servlet，下面从tomcat架构方面来分析tomcat的请求处理流程：

![image-20200521021459406](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200521021459406.png)

步骤如下：

1. Connector 组件 EndPoint 中的 Acceptor 监听客户端套接字连接并接受Socket
2. 将连接交给线程池处理，开始执行请求响应任务
3. Processor 组件读取消息保文，解析请求行、请求体、请求头，封装成 Request 对象
4. Mapper 组件根据请求行的 url 值，和请求头的 host 值，匹配由哪个 Host 容器、Context 容器、Wrapper 容器处理请求。
5. CoyoteAdapter 组件负责将 Connector 组件和 Engine容器关联起来，把生成的 Request 对象和响应对象 Response 传递到 Engine 容器中，调用Pipeline。
6. Engine 容器的管道开始处理，管道中包含若干个 Value, 每个 Value 负责部分处理逻辑。执行完 Value 后会执行基础的 Value--StandardEngineValue, 负责调用 Host 容器的 Pipeline。
7. Host 容器的管道开始处理，流程类似，最后执行 Context 容器的 Pipeline。
8. Context 容器的管道开始处理，流程类似，最后执行 Wrapper 容器的 Pipeline。
9. Wapper 容器的管道开始处理，流程类似，最后执行 Wrapper 容器对应的 Servlet 对象的处理方法。

