Spring web开发的三大容器

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

# 1 Spring

> Spring简化了开发，它整合了现有的技术，Spring是为解决软件开发的复杂性而创建的，使用简单的JavaBean替换了EJB，提供了企业应用功能，具有简单性、松耦合性、可测试性等许多优点,可以用它开发任何应用。
>
> 为了降低 Java开发的复杂性，Spring采取以下四种关键策略：
>
> + 基于POJO的轻量级和最小侵入式编程
> + 通过依赖注入和面向接口实现松耦合
> + 基于切面和惯例进行声明式编程
> + 通过切面和模板减少样板式代码
>
> ==一言蔽之：Spring是一个轻量级的控制反转( IOC )和面向切面( AOP )编程的容器框架== 

## 1.1 Spring的历史

2002年，interface21框架推出，Spring框架以该框架为原型，经过重新设计，并不断丰富其内涵，于2004年3月24日发布1.0版本，Rod Johnson 是Spring框架的创始人

## 1.2 Spring的优点

+ 免费的开源框架
+ 轻量级、非侵入式的框架
+ 支持事务处理，支持框架整合
+ 控制反转和面向切面编程

## 1.3 Spring的组成

<img src="https://images2017.cnblogs.com/blog/1219227/201709/1219227-20170930225010356-45057485.gif" alt="img" style="zoom:150%;" />

# 2 Spring的配置

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"<!--p明明空间,property-->
       xmlns:c="http://www.springframework.org/schema/c"<!--c命名空间,constructor-arg-->
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="user" class="com.sundehui.domain.User">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
    </bean>
    
</beans>
```

## 2.1Spring配置文件标签

### 2.1.1 \<beans\>

这个标签是配置文件的根标签，其他标签都在这个标签中书写

其子标签有：

1. bean
2. alias
3. beans
4. import
5. description

### 2.1.2 \<bean\>

向IOC容器注入一个POJO

```xml
<bean id="user" class="com.sundehui.domain.User"/>
```

其主要子标签为：

1. property: 为POJO的域设置初始值，但是只有该域有Setter方法才可以，否则报错
2. constructor-arg: 为POJO的构造方法传值，其方法见<a href="#1">使用有参构造的方法</a>

#### 其属性有：

1. id: 容器中对象的标识符

2. class：容器对象类型的全限定类名

3. name: 取别名，可以用空格分隔取多个别名

4. scope: 配置bean作用域，它的值有：

   + singleton/true: 单例模式，默认

   + prototype/false: 原型模式

   + request: 针对每一个http请求，创建一个新bean，同时该bean仅在当前HTTP request内有效

   + session：针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP session内有效

   + application: 

   + websocket

     > request,session,application,websocket这三个作用域需要再web.xml做额外的配置
     >
     > ```xml
     > <!--Servlet 2.4及以上的web容器-->
     > 
     >  <web-app>
     >    ...
     >   <listener>
     >       <listener-class>
     >           org.springframework.web.context.request.RequestContextListener
     >       </listener-class>
     >   </listener>
     >    ...
     > </web-app>
     > 
     > <!--Servlet2.4以前的web容器,那么你要使用一个javax.servlet.Filter的实现-->
     > <web-app>
     >  ..
     >  <filter> 
     >     <filter-name>requestContextFilter</filter-name> 
     >     <filter-class>
     >         org.springframework.web.filter.RequestContextFilter
     >      </filter-class>
     >  </filter> 
     >    
     >  <filter-mapping> 
     >     <filter-name>requestContextFilter</filter-name> 
     >     <url-pattern>/*</url-pattern>
     >  </filter-mapping>
     >    ...
     > </web-app>
     > ```
     >
     > 

   + 自定义scope, spring的作用域由接口org.springframework.beans.factory.config.Scope来定 义，自定义自己的作用域只要实现该接口即可

### 2.1.3 \<alias\>

为bean起别名，如

```xml
<bean id="user" class="com.sundehui.domain.User" name="别名1,别名2,..."/>
<alias name="user" alias="sundehui"/> <!--可以使用sundehui作为id获取容器中的对象-->
```

bean 的属性`name`也可以用于起别名，可以取多个别名，所以alias没卵用

### 2.1.4 \<import\>

一般用于多个团队开发, 它可以将其他配置文件导入:

```xml
<import resource="beans1.xml"/>
<import resource="beans2.xml"/>
<import resource="beans3.xml"/>
```

## 2.2 用Java代码进行配置

可以完全不用xml文件做配置，只用java！

JavaConfig是Spring的一个子项目，在Spring4之后，它称为了Spring的核心功能，并且为官方推荐来配置Spring。

```java
import com.sundehui.domain.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
@Configuration//表明这个类是一个配置类
@ComponentScan("com.sundehui")//开启包扫描
@Import(Config2.class)//导入另外一个配置类
public class AppConfig {

    //相当于一个bean标签，beanId为方法名, 返回值包含class属性
    @Bean
    public User getUser(){
        return new User();
    }
}

```





# 3 IOC

IOC是Spring的核心，Spring使用了多种方式实现了IOC，可以用XML配置文件，可以用注解，也可以零配置。Spring容器在初始化时先读取配置文件，根据配置文件或元数据创建与组织对象存入IOC容器，使用时再从容器中取出需要的对象。即对象的创建、管理、装配交给了Spring。

## 3.1 控制反转

对程序的控制权从程序员转为了调用者，即**获得依赖对象的方式反转**了。

> 在没有IOC的程序中，对象的创建硬编码在程序中，对象的创建由该程序自己进行，控制反转后将对象的创建转移给第三方。如下图，ioc的存在实现了图2的解耦过程。

![image-20200302113122839](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200302113122839.png)

## 3.2 DI（依赖注入）

依赖注入是IOC的实现方式

### 3.2.1 什么是依赖注入？

#### 3.2.1.1 依赖

bean对象的创建依赖于容器

#### 3.2.1.2 注入

bean对象的域由容器注入

##### 注入方式有三种

1. 构造器注入

2. **setter注入**

   > ```java
   > public class Student {
   >     private String name;
   >     private User user;
   >     private String[] books;
   >     private List<String> hobbys;
   >     private Map<String,String> card;
   >     private Set<String> games;
   >     private Properties info;
   >     private String couple;
   >     
   >     setters...;
   >     getters...;
   > }
   > ```
   >
   > ```xml
   > <bean id="user" class="com.sundehui.domain.User"/>
   > <bean id="student" class="com.sundehui.domain.Student">
   > <!-- 普通值注入， value-->
   >     <property name="name" value="孙德辉"/>
   > <!-- bean注入，ref-->
   >     <property name="user" ref="user"/>
   > <!-- 数组注入-->
   >     <property name="books">
   >         <array>
   >             <value>《鲁宾孙漂流记》</value>
   >             <value>《钢铁是怎样炼成的》</value>
   >             <value>《呐喊》</value>
   >         </array>
   >     </property>
   > <!-- list注入-->
   >     <property name="hobbys">
   >         <list>
   >             <value>看电视</value>
   >             <value>看书</value>
   >             <value>打游戏</value>
   >         </list>
   >     </property>
   > <!-- Map注入-->
   >     <property name="card">
   >         <map>
   >             <entry key="idCard" value="13432123323432123432"/>
   >             <entry key="moneyCard" value="13432123323432123432"/>
   >         </map>
   >     </property>
   > <!-- Set注入-->
   >     <property name="games">
   >         <set>
   >             <value>王者荣耀</value>
   >             <value>cod</value>
   >         </set>
   >     </property>
   > <!-- 空指针注入-->
   >     <property name="couple">
   >         <null/>
   >     </property>
   > <!-- properties注入-->
   >     <property name="info">
   >         <props>
   >             <prop key="学号">1611650716</prop>
   >             <prop key="性别">male</prop>
   >         </props>
   >     </property>
   > </bean>
   > ```
   >
   > 

3. 拓展注入

   ```xml
   xmlns:p="http://www.springframework.org/schema/p"<!--p明明空间,property-->
   xmlns:c="http://www.springframework.org/schema/c"<!--c命名空间,constructor-arg-->
   ```

   

### 3.2.2 使用哪种方式注入？

==it is a good rule of thumb to use constructors for mandatory dependencies and setter methods or configuration methods for optional dependencies.Note that use of the [@Required](https://docs.spring.io/spring/docs/5.2.4.RELEASE/spring-framework-reference/core.html#beans-required-annotation) annotation on a setter method can be used to make the property be a required dependency; however, **constructor injection with programmatic validation** of arguments is preferable.==

### 3.2.3 循环依赖

https://blog.csdn.net/qq_36381855/article/details/79752689

## 3.3 IOC创建对象的方式

1. 使用无参构造方法（默认）。

2. <p name="1">使用有参构造方法：</p>

   ```xml
   <bean id="user" claass="com.sundehui.domain.User">
       <!--第一种方法，使用参数列表的下标-->   
       <constructor-arg index="0" value="sundehui"/>
       <!--第二种方法，使用参数列表的类型, 不建议使用-->
       <constructor-arg type="java.lang.String" value="sundehui"/>
       <!--第二种方法，使用参数列表的形参名-->
       <constructor-arg type="name" value="sundehui"/>
   </bean>
   ```

   

## 3.4 IOC创建对象的时机

配置文件被加载后就创建了对象，从容器中取出的对象都是同一个，即应用了单例模式。



# 4 bean的自动装配

+ 自动装配是Spring满足bean依赖的一种方式
+ Spring会在上下文中自动寻找，并自动给bean装配属性

## 4.1 装配的三种方式

### 4.1.1 xml中显式配置

### 4.1.2 Java代码中显式配置

### 4.1.3 隐式自动装配

#### 基于xml的自动装配

+ byType

  ![image-20200302193733077](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200302193733077.png)

+ byName![image-20200302193755127](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200302193755127.png)

#### 基于注解的自动装配

1. 导入约束

   `xmlns:context="http://www.springframework.org/schema/context"`

   ```xml
   http://www.springframework.org/schema/context 
   https://www.springframework.org/schema/context/spring-context.xsd"
   ```

2. ==配置注解的支持==

   `<context:annotation-config/>`

3. 注解

   + **@Autowired**：一般放在域或方法上

     它的作用是，自动把**在IOC容器中的对象**给当前类的域装配，要求当前类的域名和IOC的对象id一样，是byName的，它要求域不为空，如果可以为空，可将其required属性设置为false

   + **@Qualifier(value="beanId")**：和@Autowired组合使用，显示指出为注解的域装配那个对象

   + **@Resource**:Java的原生注解，它默认用byName，找不到名字用byType方式装配bean，也可以用name属性指定bean



# 5 Spring的注解开发

+ 装配注解见上文
+ @Component：放在类上，说明说明这个类被Spring管理了
+ @Value：为被@Compent注解的类装配属性值，放在域或setter上
+ @Component 的衍生注解
  + @Repository  --- dao层
  + @Service  --- service层
  + @Controller --- controller层
+ @Scope：配置bean的作用域



# 6 代理模式

即真实角色委托代理角色去做真实角色想做的事

## 6.1 角色分析

+ 抽象角色：一般用接口
+ 真实角色：被代理的角色
+ 代理角色：代理真实角色，它一般做一些附属操作
+ 客户角色：访问代理对象的角色

## 6.2 静态代理

