# 1 Tomcat

server.xml是tomcat的核心配置文件

![image-20200305231646612](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200305231646612.png)

## 1.1 网站是如何访问的

1. 浏览器输入url，回车

2. 检查本机C:\Windows\System32\drivers\etc\hosts文件中有没有该域名的映射ip

   2.1 如果有，返回其对应的ip，进行访问

   2.2 如果无，去DNS服务器查找，找到则返回其ip给本机，本机访问，找不到404

   

# 2 Http

Http是一个运行在Tcp上的请求-响应协议

它指定了客户端给服务器发送消息的格式，同时也指定了响应信息的格式

请求和响应的头都以Ascii码的形式给出

默认端口为： **80**



# 3 Servlet原理

## 3.1 servlet调用过程

servlet是由web服务调用的，其调用过程如下图

![image-20200305235410954](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200305235410954.png)

## 3.2 servlet mapping

指定的servlet mapping的优先级最高

# 4 ServletContext对象

Web容器在启动的时候会为每个web应用创建一个ServletContext对象，它代表了当前的web应用

在servlet中获取它`this.getServletContext()`

作用：

+ 在同一个项目的多个servlet中共享数据，即跨会话共享数据

+ 获取初始化参数，即通过`getInitParam()`从web.xml中获取`context-param`标签中的`param-name`标签的内容对应的初始化参数，

+ 获取请求转发对象

  ```java
  //SpringMvc获取servletContext对象的方法
  ServletContext servletContext = 
      request.getSession().getServletContext();
  //获取请求转发对象
  RequestDispatcher dispatcher =
              servletContext.getRequestDispatcher("转发路径");
  //转发请求和响应
  dispatcher.forward(request,response);
  ```

+ 读取资源文件

  ```java
  InputStream filePath = servletContext.getResourceAsStream("文件路径");
  ```

  

# 5 HttpServletResponse

可以用来下载文件，可以生成验证码图片给浏览器，主要的用处就是重定向

```java
response.sendRedirct("重定向路径")//302状态码
//这句代码是以下两句代码的简化
response.setHeader("Location","重定向路径");
response.setStatus(302);

//重定向路径如果以 / 开头，则会从web容器的根路径找起，如果容器中有多个应用，或者当前应用的url不是以 / 开头的，则会404，如果仅仅指定裸路径，如：index.jsp, 则会从当前路径的父路径下开始找，
//解决的办法有两种：
//1. 显示表明项目名, 如: /springweb/index.jsp
//2. 动态获取项目名, 如: request.getContextPath()+"/index.jsp"
```



# 6 HttpServletRequest

用于获取请求传递的数据，转发

```java
request.getRequestDispatcher("转发路径").forward(req,resp); // 307状态码
//转发路径以 / 开头，/表示当前项目的根目录，访问index.jsp可以为/index.jsp，访问其他jsp页面可以是
// /WEB-INF/pages/test.jsp
//如果不加/，默认在当前路径的父路径下寻找，切记这里的路径仅指url路径
```

`HttpRequest`的`getContextPath()`方法可以获取当前应用的根目录, 例如

![image-20200306021740800](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200306021740800.png)

当前目录的根目录为/springweb，访问它的资源的url为`http://localhost:8080/springweb/**`

# 7 会话

*会话* 是指一个终端用户与交互系统进行通讯的过程，比如从输入账户密码进入操作系统到退出操作系统就是一个*会话*过程。*会话* 较多用于网络上，TCP的三次握手就创建了一个*会话*，TCP关闭连接就是关闭*会话*

会话用来保存访问记录

会话是由一组请求和响应组成，这些请求和响应是围绕一件事进行的，所以这些请求与响应之间需要有数据传输，即需要进行会话状态的跟踪，但是http协议是无状态的，在不同的请求之间无法传递数据，因此就需要在请求间传递数据的会话跟踪技术，这种技术技术为Cookie和Session

## 7.1 Cookie

在Java中是一个类，位于`javax.servlet.http`包下

### 7.1.1 概述

是一种进行网络会话状态跟踪的技术，客户第一次访问服务器时，由服务器生成，封装在响应头中，浏览器接受响应后将Cookie保存在客户端，当客户端再次发送**同类请求**，会在请求中携带cookie数据，发送到服务器，服务器对会话实现了跟踪，cookie不是Java专有的技术，它是一种web技术，是所有web开发语言都支持的技术

**所谓同类请求就是资源路径一样的一类请求**

### 7.1.2 详述

cookie是由若干键值对组成，其键值都是字符串

#### 7.1.2.1 创建Cookie

```java
Cookie cookie = new Cookie("key","value");
cookie.getName(); //name,获取cookie存的键名
cookie.getValue();//value，获取cookie存的值
response.addCokie(cookie);//向响应中添加cookie
```

#### 7.1.2.2 为cookie设置路径

```java
cookie.SetPath(reqest.getContextPath()+"/aa/bb");
```

当再次访问服务器，且资源路径为/aa/bb/xxx时，将会携带上该cookie

==为cookie设置路径时必须指出当前项目名：request.getContextPath()==

#### 7.1.2.3 设置cookie的有效期

```java
cookie.setMaxAge(int expiry);//单位为秒
```

+ expiry大于零，表示将cookie存到硬盘
+ expiry小于零等同于不设置，即默认存到缓存
+ ==expity=0，表示该cookie失效==

#### 7.1.2.4 服务端获取并解析cookie

```java
Cookie[] cookies = req.getCookies();
for (Cookie cookie : cookies) {
    String name = cookie.getName();
    String value = cookie.getValue();
    System.out.println("name:"+name+", value:"+value);
}
```





## 7.2 Session

在javaweb的api中，session是以`javax.servlet.http.HttpSession`的接口对象的形式呈现的

==在访问jsp是会创建Session==

## 7.2.1概述

服务端记录会话，同cookie一样，它也是一种会话跟踪技术，只是将会话状态保存到了服务端

什么“会话”呢？会话表示从打开浏览器发出第一次请求开始，到关闭浏览器，这个过程就是一次会话，它也不是javaweb特有的，而是整个web开发中所共用的技术

## 7.2.2 详述

#### 7.2.2.1 Session的创建

```java
request.getSession();//如果当前请求有session，则使用之，否则创建一个新的session
request.getSession(boolean create)//create为true同上，为false，如果当前request有session，则使用之，否则返会null
```

**为何会有两个获取Session的方法呢？**

> 用request.getSession()或者request.getSession(true)这两种方法创建Session的目的是存数据，无论当前请求有没有session，都需要一个Session存储数据
>
> 用request.getSession(false)获取Session的目的是取数据，如果当前request没有Session，那么也没必要创建一个新的session，再从里边取数据

#### 7.2.2.2 Session的使用

> session是一个专门用于存放数据的集合，类似于HashMap, 一般称这个用于存放数据的内存空间为域属性空间，HttpSession有三个方法专门对域中的数据进行读写操作的

```java
session.setAttribute(String name, Object value);//存放键值对
session.getAttribure(String name);//通过键获取值
session.removeAttribure(String name);//移除键值对
```

#### 7.2.2.3 Session的原理

在服务器中会为每一个会话维护一个Session，不同的会话对应不同的Session对象，那么服务器是怎么识别Session的呢？如何做到在同一会话过程中一直使用的是同一个Session对象呢？

服务器对当前应用的Session是以map的形式进行管理的，这个map称为**Session列表**。其key是一个128位长度的随机串，称其为JSESSIONID，value为session对象的引用。

1. 客户端访问服务器，第一次执行到request.getSession()时，创建一个Map.Entry对象，以JSESIONID(随机串)为键，session对象的引用为值，放入Session列表中
2. 服务器以JSESSION为name，以JSESSION的值(随机串)为value，生成一个cookie对象，并将其放入响应头中，发送给客户端
3. 客户端接收到这个cookie后，会将其保存到缓存中，再次发送请求时，会携带这个cookie
4. 服务器接受到cookie后，到Session列表中进行查找，找到后返回JSESSIONID对应的Session对象引用

#### 7.2.2.4 Session的失效

session在某个时间一直未被访问，该session将超时进而失效，默认的超时时间为30分钟，可以在web.xml中自行配置session的超时时间

```xml
<session-config>
    <session-timeout>20</session-timeout><!--单位为分钟-->
</session-config>
```

即使还未超时，也可以手动设置为失效:`session.invalidate()`

# 8 域属性空间范围对比

在javaweb的api中，存在三个可以存放属性的**空间范围对象**，这三个对象所存储属性的作用范围从大到小为：

`ServletContext` ---> `HttpSession` --->  `HttpServletRequest`

==对这三个域属性空间的使用原则是，在不影响功能的前提下，尽可能使用小的范围，一来节省性能，二来保证数据安全==

## 8.1 ServletContext

即application，置入其中的域属性是整个应用范围的，可以完成跨会话共享数据，其生命周期为应用的生命周期

## 8.1 HttpSession

置入其中的域属性是会话范围的，可以跨请求共享数据

## 8.2 HttpServletRequest

置入其中的域属性是请求范围的，可以跨Servlet共享数据，但是这些servlet必须在同一请求中，即这些servlet是请求转发的















