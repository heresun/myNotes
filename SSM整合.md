# Spring web开发的三大容器

所谓容器就是管理对象的地方，博客链接

https://www.cnblogs.com/liujia1990/p/9024884.html

## spring容器

管理service和dao的容器，因此在spring的配置文件中不扫描带有`Controller`注解的类，对应`applicationContext.xml`文件

## springMVC容器

管理controller的容器，因此在springMVC的配置文件中只扫描带有`Controller`注解的类，并且springMVC的拦截器也是由其管理的，如：

```xml
<mvc:interceptors>
  <mvc:interceptor>
   <mvc:mapping path="/employee/**" ></mvc:mapping>
   <bean class="com.smart.core.shiro.LoginInterceptor" ></bean>
  </mvc:interceptor>
</mvc:interceptors>
```

对应`springMVC.xml`文件

> spring容器是springMVC的父容器，后者可以访问前者的bean，前者不可以访问后者，因此在controller中访问service时需要将其注入springMVC容器

## web容器

管理servlet、Listener、Filter的容器，他们都是在web容器的掌控范围内，但是不在以上两个容器的掌控范围，因此无法在这些类中直接使用spring注解的方式注入需要的对象，容器无法识别。

但我们有时候又确实会有这样的需求，比如在容器启动的时候，做一些验证或者初始化操作，这时可能会在监听器里用到bean对象；又或者需要定义一个过滤器做一些拦截操作，也可能会用到bean对象。但是前提是：servelt容器在实例化监听器或过滤器对象时，要确保spring容器已经初始化完成。

而spring容器的初始化也是由Listener（ContextLoaderListener）完成，因此只需在web.xml中先配置初始化spring容器的Listener（见ssm整合的第五项），然后在配置自己的Listener。

# SSM整合

1. 创建一个maven项目

2. pom.xml文件添加依赖

   ```xml
   //用于固定版本
   <properties>
       <project.build.sourceEncoding>UTF- 8</project.build.sourceEncoding>
       <maven.compiler.source>1.7</maven.compiler.source>
       <maven.compiler.target>1.7</maven.compiler.target>
       <spring.version>5.2.1.RELEASE</spring.version>
       <slf4j.version>1.7.25</slf4j.version>
       <log4j.version>2.12.1</log4j.version>
       <mysql.version>5.1.47</mysql.version>
       <mybatis.version>3.5.2</mybatis.version>
     </properties>
   
     <dependencies>
       <!--单元测试框架-->
       <dependency>
         <groupId>junit</groupId>
         <artifactId>junit</artifactId>
         <version>4.11</version>
         <scope>compile</scope>//此时可以在任何java代码中使用单元测试
       </dependency>
   <!--    aop编织框架-->
       <dependency>
         <groupId>org.aspectj</groupId>
         <artifactId>aspectjweaver</artifactId>
         <version>1.9.2</version>
       </dependency>
   <!--    aop框架-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-aop</artifactId>
         <version>${spring.version}</version>
       </dependency>
   <!--    spring上下文框架-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-context</artifactId>
         <version>${spring.version}</version>
       </dependency>
   <!--    spring-webmvc的依赖，它支持了远程调用和远程服务-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-web</artifactId>
         <version>${spring.version}</version>
       </dependency>
   <!--    依赖spring-web,增加了对restful的支持-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-webmvc</artifactId>
         <version>${spring.version}</version>
       </dependency>
   <!--    spring测试框架-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-test</artifactId>
         <version>${spring.version}</version>
       </dependency>
   <!--    spring对事务的支持-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-tx</artifactId>
         <version>${spring.version}</version>
       </dependency>
   <!--    spring对jdbc的支持-->
       <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-jdbc</artifactId>
         <version>${spring.version}</version>
       </dependency>
   <!--    数据库链接驱动-->
       <dependency>
         <groupId>mysql</groupId>
         <artifactId>mysql-connector-java</artifactId>
         <version>${mysql.version}</version>
       </dependency>
   <!--    对servlet的支持-->
       <dependency>
         <groupId>javax.servlet</groupId>
         <artifactId>servlet-api</artifactId>
         <version>2.5</version>
         <scope>provided</scope>
       </dependency>
   <!--    对jsp的支持-->
       <dependency>
         <groupId>javax.servlet.jsp</groupId>
         <artifactId>jsp-api</artifactId>
         <version>2.0</version>
         <scope>provided</scope>
       </dependency>
   <!--    jsp的标签库-->
       <dependency>
         <groupId>jstl</groupId>
         <artifactId>jstl</artifactId>
         <version>1.2</version>
       </dependency>
   <!--    引入日志框架-->
       <dependency>
         <groupId>org.apache.logging.log4j</groupId>
         <artifactId>log4j-core</artifactId>
         <version>${log4j.version}</version>
       </dependency>
       <dependency>
         <groupId>org.slf4j</groupId>
         <artifactId>slf4j-api</artifactId>
         <version>${slf4j.version}</version>
       </dependency>
       <dependency>
         <groupId>org.slf4j</groupId>
         <artifactId>slf4j-log4j12</artifactId>
         <version>${slf4j.version}</version>
       </dependency> <!-- log end -->
       <dependency>
         <groupId>org.mybatis</groupId>
         <artifactId>mybatis</artifactId>
         <version>${mybatis.version}</version>
       </dependency>
       <dependency>
         <groupId>org.mybatis</groupId>
         <artifactId>mybatis-spring</artifactId>
         <version>1.3.0</version>
       </dependency>
    
   	<dependency>
       	<groupId>com.mchange</groupId>
       	<artifactId>c3p0</artifactId>
       	<version>0.9.5.2</version>
   	</dependency>
   
   
     </dependencies>
   ```

3. 创建`applicationContext.xml`文件，此文件为Spring的配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xmlns:tx="http://www.springframework.org/schema/tx"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
   	http://www.springframework.org/schema/beans/spring-beans.xsd
   	http://www.springframework.org/schema/context
   	http://www.springframework.org/schema/context/spring-context.xsd
   	http://www.springframework.org/schema/aop
   	http://www.springframework.org/schema/aop/spring-aop.xsd
   	http://www.springframework.org/schema/tx
   	http://www.springframework.org/schema/tx/spring-tx.xsd">
   
       <!--开启注解的扫描，希望处理service和dao，controller不需要Spring框架去处理-->
       <context:component-scan base-package="com.sundehui" >
           <!--配置哪些注解不扫描-->
           <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller" />
       </context:component-scan>
   </beans>
   ```

4. 创建`springMVC.xml`文件，此为springmvc的配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="
           http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/mvc
           http://www.springframework.org/schema/mvc/spring-mvc.xsd
           http://www.springframework.org/schema/context
           http://www.springframework.org/schema/context/spring-context.xsd">
       <!--开启注解扫描，只扫描Controller注解-->
       <context:component-scan base-package="com.sundehui">
           <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
       </context:component-scan>
   
       <!--配置的视图解析器对象-->
       <bean id="internalResourceViewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/pages/"/>
           <property name="suffix" value=".jsp"/>
       </bean>
       <!--过滤静态资源-->
       <mvc:resources location="/css" mapping="/css/**"/>
       <mvc:resources location="/images/" mapping="/images/**"/>
       <mvc:resources location="/js/" mapping="/js/**"/>
       <!--开启SpringMVC注解的支持-->
       <mvc:annotation-driven/>
   </beans>
   
   ```

5. 修改`web.xml`文件，添加前端控制器，Spring监听ServletContext对象的创建,一旦创建监听器就加载spring的配置文件

   >  <img src="https://img-blog.csdnimg.cn/20190902171005382.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ0NTQzNTA4,size_16,color_FFFFFF,t_70" alt="img" style="zoom:50%;" />

   ```xml
   <web-app>
       <display-name>Archetype Created Web Application</display-name>
       <!--  配置过滤器解决乱码-->
       <filter>
           <filter-name>characterEncodingFilter</filter-name>
           <filter-class>
               org.springframework.web.filter.CharacterEncodingFilter
           </filter-class>
           <init-param>
               <param-name>encoding</param-name>
               <param-value>UTF-8</param-value>
           </init-param>
       </filter>
       <filter-mapping>
           <filter-name>characterEncodingFilter</filter-name>
           <url-pattern>/*</url-pattern>
       </filter-mapping>
       <!--  配置前端控制器-->
       <servlet>
           <servlet-name>dipatcherServlet</servlet-name>
           <servlet-class>
               org.springframework.web.servlet.DispatcherServlet
           </servlet-class>
           <!--  在服务器启动时加载springMVC.xml配置文件-->
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springMVC.xml</param-value>
           </init-param>
           <!--启动服务器时创建该servlet-->
           <load-on-startup>1</load-on-startup>
       </servlet>
       <servlet-mapping>
           <servlet-name>dipatcherServlet</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
       <!--  配置Spring的监听器，监听ServletContext对象的创建
       默认情况下只加载WEB-INF目录下的applicationContext.xml配置文件,这里是整合Spring和SpringMVC     的关键-->
       <listener>
           <listener-class>
               org.springframework.web.context.ContextLoaderListener
           </listener-class>
       </listener>
   	<!--设置配置文件的路径,会这样就可以不必将applicationContext.xml文件放在WEB-INF下了-->
       <context-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:applicationContext.xml</param-value>
       </context-param>
   </web-app>
   
   ```

6. 至此已将SpringMVC整合进Spring，**其表现为：在controller(SpringMVC)中能够成功调用service(Spring)中的方法**

   > 以上说明Spring 和 SpringMVC各有一个容器，spring的容器管理service和dao，SpringMVC的容器管理controller

7. mybatis环境搭建很简单，首先创建一个`mybatisConfig.xml`文件，在使用时读入该配置文件，并创建`SqlSessionFactory`和`SqlSession`两个对象，然后用`SqlSession`对象获取dao的代理对象，代码如下

   ```java
   InputStream in = Resources.getResourcesAsStream("mybatisConfig.xml");
   SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(in);
   SqlSession session = factory.openSession();
   UserDao dao = session.getMapper(UerDao.class);
   ```

8. 将mybatis整合进spring，就是把`mybatisConfig.xml`文件的内容放入`applicationContext.xml`中

   > 怎样才算整合成功呢？在service中能够调用dao对象，这个对象是由spring容器管理的dao接口的代理对象

   1. 在`applicationContext.xml`中配置数据库连接池

      ```xml
      
      ```

      

   2. 