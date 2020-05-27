# 什么是MVC

+ M(Model):数据模型,提供要展示的数据, 包含数据和行为,具体是dao和damain,service
+ V(View):视图层, 负责数据的展现, 具体是jsp,html等
+ C(Controller): 控制层, 负责请求与相应, 将接受的请求委托给模型处理, 将处理的结果相应给视图

MVC降低了视图与业务逻辑间的双向耦合, 是一种架构模式



# 1 SpringMVC

SpringMVC是基于Java实现的MVC的轻量级的web框架, 

## 1.1  优点

1. 轻量
2. 高效, 基于请求响应的MVC框架
3. 与Spring无缝结合
4. 约定优于配置
5. 功能强大: RestFul 数据验证 格式化 本地化等
6. 简洁灵活

## 1.2 DispatcherServlet

SpringMVC是围绕DispatcherServlet设计的, 它的作用是将请求分发到不同的处理器. SpringMVC 以请求为驱动, 围绕一个中心servlet分派请求及提供其他功能, DispatcherServlet是一个实际的servlet, 它继承了servlet基类(HttpServlet),

## 1.3 SpringMVC的请求传递过程 

在下图中,实线部分是SpringMVC已经实现的, 用户只需实现虚线部分即可

<img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200304201056252.png" alt="image-20200304201056252" style="zoom:80%;" />

### 1.3.1 流程分析

假设浏览器发出一个请求,url为`http://localhost:8080/springweb/hello`

1. `DispatcherServlet`为一个前端控制器,是SpringMVC的控制中心, 用于接收并拦截请求
2. `HandlerMapping`为处理器映射, `DispatcherServlet`调用它, 它根据请求的url查找Handler
3. `HandlerExecution`为具体的Handler, 它的作用是根据url查找控制器, 如上的url被查找控制器为: hello
4. `HandlerExecution`将解析的信息传递给`DispatcherServlet`, 如解析控制器映射等
5. `HandlerAdapter`为处理器适配器, 它按照一定的规则执行Handler
6. Handler让具体的`Controller`执行
7. `Controller`将执行的结果反馈给`HandlerAdapter`, 如ModleAndView
8. `HandlerAdapter`将视图逻辑名或者模型传递给`DispatcherServlet`
9. `DispatcherServlet`调用视图解析器`ViewResolver`来解析`HandlerAdapter`传回的视图名
10. `ViewResolver`将解析的逻辑视图名传给`DispatcherServlet`
11. `DispatcherServlet`根据视图解析器传回的结果调用具体的视图
12. 将视图呈现给用户

# 2 SpringMVC的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

     <context:component-scan base-package="com.sundehui"/>
    <!--支持MVC的注解驱动
			在SpringMVC中一般采用@RequestMapping注解玩完成映射关系
			要想使其生效,必须向上下文中注册:
			DefaultAnnotationHandlerMapping
			AnnotationMethodHandlerMapping这两个实例,
			分别支持类级别和方法级别的注解
			annotation-driven帮助我们自动注册这两个实例
			注入的这两个bean可以解决jackson json乱码问题-->
    <mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="UTF-8"/>
            </bean>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper">
                    <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                        <property name="failOnEmptyBeans" value="false"/>
                    </bean>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
    
    <!--1 处理器映射器-->
     <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
    
    <!--2 处理器适配器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
    
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver" />

    <!--3 视图解析器-->
    <bean id="internalResourceViewResolver" 
          class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--4 过滤静态资源,即不让springMVC处理静态资源, 有以下两种方法-->
    <!--4.1, 简便配置-->
    <mvc:default-servlet-handler/>
    <!--4.2, 手工配置-->
    <mvc:resources mapping="/css/**" location="/css/"/>
    <mvc:resources mapping="/js/**" location="/js/"/>
    <mvc:resources mapping="/images/**" location="/images/"/>

  

</beans>
```

# 3 RestFul风格

Restful是一种资源定位及资源操作的风格, 不是标准和协议, 只是一种风格, 基于这个风格设计的软件可以更加简洁, 更有层次, 更易于实现缓存等机制

## 使用方法一:

使用语义注解

```java
@GetMapping("/user/finAll")
```

## 使用方法二:

使用显式注解

```java
@RequestMapping(value = "/test",method = RequestMethod.GET)
```

# 4 重定向和转发

controller默认是将数据转发到jsp,

如果不配置视图解析器, 也可以用`return "foward:/WEB-INF/pages/test.jsp"`访问, 

或者`return "redirct:/WEB-INF/pages/test.jsp"`, 进行重定向

==当然,这种方法不推荐==

# 5 接受url参数数据及数据回显

## 情况一: 传递零散的参数

这种情况下, url应该是`http://localhost:8080/springweb/user/test?name=sundehui`, 如果参数名不是name, 则该参数为其类型的零值

<img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200304235845575.png" alt="image-20200304235845575" style="zoom:80%;" />

可以如下, 使用`@RequestParam`显式指定一个参数名, 如果参数名不是username, 前端显示400错误, 后端不处理请求

![image-20200305000114909](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200305000114909.png)

## 情况二:传递一个对象

假设向后台传递一个user对象, 其字段名有 id, name, money, url如下`http://localhost:8080/springweb/user/t2?id=1&name=sundehui&money=100`

如果出现参数名和对象中的字段名不一样, 则该字段为该字段类型的零值, 并且如果url中传递的参数是对象中没有的, 该参数键值对不会被获取到

![image-20200305000959821](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200305000959821.png)

# 6 解决乱码

在web.xml里配置如下

```xml
<filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```



# 7 JSON

在前后端分离的情况下进行数据传输

==前后端分离并使用json传输数据的时候, 要在controller的方法上加上`@ResponseBody`这个注解, 这样返回的json字符串就不会视图解析器解析, 在controller的类上加上`@RestController`这个注解就不需要再加`@ResponseBody`这个注解==

使用json的时候最好在`@RequestMapping`中增加参数` produces = "application/json;charset=utf-8"`, 指定json字符串的编码为utf-8, 也可以在spring-mvc.xml中增加配置如下

```xml
<mvc:annotation-driven>
   <mvc:message-converters register-defaults="true">
       
     <bean 
                class="org.springframework.http.converter.StringHttpMessageConverter">
        <constructor-arg value="UTF-8"/>
      </bean>
       
      <bean 
 class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
         <property name="objectMapper">
             <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                   <property name="failOnEmptyBeans" value="false"/>
              </bean>
          </property>
       </bean>
   </mvc:message-converters>
</mvc:annotation-driven>
```



## jackson使用问题

必须导入如下三个包才可以

```xml
     <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.10.0</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.10.0</version>
        </dependency>
        <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.10.0</version>
        </dependency>
```



# 8 拦截器





类似于过滤器, 用于对请求预处理和后处理, 是AOP思想的具体应用

拦截器是SpringMVC中的概念, 拦截器只会拦截访问控制器的请求

## 8.1 创建拦截器

1. 自定义类实现`HandlerInterceptor`接口, 实现其方法

   ![image-20200305035834652](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200305035834652.png)

   preHandle方法的返回值为false时, 拒绝放行, 为true, 放行

   三个方法的执行顺序:

   1. preHandle
   2. 被切入的方法
   3. postHandle
   4. afterCompletion

2. 在spring-mvc.xml中配置拦截器

   ![image-20200305040114922](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200305040114922.png)

#  9 文件上传

1. 添加jar包

```java
<dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.3</version>
        </dependency>
```

2. 添加form表单

```html
<form action="${pageContext.request.contextPath}/user/upload" 
      enctype="multipart/form-data" method="post">
    <input type="file" name="file"/>
    <input type="submit" value="upload"/>
  </form>
```

3. 在spring-mvc.xml中添加如下配置

   ```xml
   <!--    配置文件上传-->
       <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
   <!--        配置默认编码-->
           <property name="defaultEncoding" value="UTF-8"/>
   <!--        配置上传的文件大小-->
           <property name="maxUploadSize" value="10485760"/>
           <property name="maxInMemorySize" value="40960"/>
       </bean>
   
   ```

4. 代码

   ```java
   @RequestMapping("/upload")
       public String test2 (@RequestParam("file")CommonsMultipartFile file, HttpServletRequest request, HttpServletResponse response, Model model) throws IOException, ServletException {
           //获取源文件名
           String filename = file.getOriginalFilename();
           //如果源文件名为空, 提示
           if ("".equals(filename)){
               model.addAttribute("msg", "请添加文件");
               return "index";
           }
           System.out.println("uploadFileName:"+filename);
           //上传路径保存位置
           String realPath = request.getServletContext().getRealPath("/upload");
           File file1 = new File(realPath);
           if (!file1.exists()){
               file1.mkdir();
           }
           System.out.println("上传文件的保存地址为："+realPath);
           //从请求的文件中获取输入流
           InputStream fins =file.getInputStream();
           //新建一个输出流
           FileOutputStream fos = new FileOutputStream(new File(file1,filename));
   
           //存入本地
           int len = 0;
           byte[] buffer = new byte[1024];
           while ((len=fins.read(buffer))!=-1){
               fos.write(buffer,0, len);
               fos.flush();
           }
           fins.close();
           fos.close();
           return "test";
       }
   ```

   