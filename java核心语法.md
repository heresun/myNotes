# 面向对象

+ 面向过程编程：把模型分解成一步一步的过程
+ 面向对象编程：通过对象的方式，把现实世界映射到计算机模型的一种编程方法。

## 多态

针对某个类型的方法调用，其真正执行的方法取决于运行时期**实际类型**的方法。

一般用于指向子类型对象的父类型引用调用子类型对象的方法。即**实际类型**就是子类型。

## 接口

Java的接口指：一个接口类型和一组方法签名。

接口中的字段必须用`public static final`修饰、

在使用的时候，实例化的对象永远只能是某个具体的子类，**但总是通过接口去引用它**，因为接口比抽象类更抽象

**接口的作用什么？**

标准化，规范化，解耦

**接口可以继承接口么？**

可以

**接口中的方法可以重载么？**

可以

**接口为什么可以起到解耦的作用？**

接口是一种标准，它定义了一组操作，但它只关心这些操作，而不关心每个操作的具体实现，所有实现这个接口的类都必须遵守标准即实现这些方法。我们在使用接口的时候并不知道这个接口要被哪些类所实现啊，但我们知道凡是实现了这个接口的类必定实现了这一组操作。

如果一个类不能确定它最后的类型，就是说不知道它以后要被实现成什么样，就可以采用面向接口的编程。所有需要这个类的地方都设成一个接口，而让这个类继承这个接口。后期要更改的时候只用继承这个接口就可以了。这样使用到这一类类的地方就不依赖于一个具体的类了，进而实现了解耦。

```java
// 定义一个接口 磁盘
interface Disk(){
  // 保存文件
  void save(File file);  
}

// U盘实现
class UDisk implement Disk{
 void save(File file);  
}
// 硬盘实现
class HardDisk implement Disk{
 void save(File file);  
}


// 下载类
class Download{
     //这里用接口声明，因为我们不知道，也不用知道，我们未来会存到什么样的磁盘，我们不依赖于任何类型的磁盘，我们只依赖于这个接口
	Disk disk;
    // 调用保存文件方法
    void download(File file){
        disk.save(file);
    }

    //具体接口的实现
    void setDisk(Disk disk){
        this.disk=disk;
    }
}


 public static void main(String[] args){
 	  // 实例化下载类
      Download download = new Download();
      
      // 设置存储目标是U盘
      download.setDisk(new UDisk());
      // 文件被存到了U盘
      download.download(file);
    
      // 设置存储目标为硬盘
      download.setDisk(new HardDisk());
      // 文件被存到了硬盘
      download.download(file);
	  
	  /*
	  *  某天我们想把下载的文件保存到CD里边，我们只需要增加CDDisk类，实现Disk接口就可以不对download本身做任何修改，
	  * 就可以方便的将文件下载到CD或其他介质里。我们的Download类不依赖于任何具体的类，这样就解除了与任何具体存储设备的耦合！
	  */
  }
```



**default方法的作用是什么？**

当我们需要给接口新增一个方法时，会涉及修改全部子类，但是如果是default方法就不用全部修改，只需要在需要覆写的地方覆写default方法。

default方法虽然跟可以有逻辑代码，但是不同于普通方法，它不能访问字段，因为接口中没有实例字段。

## 作用域

+ public

  > 可以被其他任何类访问

+ private

  > 仅仅可以被本类访问
  >
  > 内部类可以访问外部类的private

+ protected

  > 作用于继承关系
  >
  > 可以被子孙类访问

+ 包作用域（默认）

  > 一个类允许访问同一个包下没有用public和private修饰的字段和方法，可以访问同一个包下被protected修饰的字段和方法

## 核心类

1. String

   > + String内部是通过维护一个char数组表示的。
   >
   > + 它的一个最大的特点就是**不可变**，这种不可变是通过内顾的`private final char[]`以及没有任何修改`char[]`的方法实现的。
   >
   > + 字符串比较
   >
   >   > ```java
   >   > String s1 = "hello";
   >   > String s2 = "hello";
   >   > //s1==s2结果为true，s1.equals(s2)也为true
   >   > String s1 = new String("hello");
   >   > String s2 = new String("hello");
   >   > //s1==s2结果为false，s1.equals(s2)为true
   >   > 
   >   > //比较时忽略大小写
   >   > s1.equalsIgnoreCase(s2)
   >   > ```
   >
   > + 子串搜索
   >
   >   + `s1.contains(s2)  boolean`
   >
   >     `contains()`方法的参数是`CharSequence`,它是`String`的父类
   >
   >   + `s1.indexOf("l")`,返回“l”的索引，左数第一个
   >
   >   + `s1.indexOf("l")`,返回“l”的索引，右数第一个
   >
   >   + `s1.startWith("He")`,是否以“He”开头，返回布尔类型
   >
   >   + `s1.endWith("lo")`,是否以“lo”结尾，返回布尔类型
   >
   >   + `s1.substring(2,4)`,提取子串，左闭右开区间
   >
   > + 去除首位空白字符
   >
   >   + `s1.trim()`这个方法没有改变原字符串，返回了一个新的字符串
   >   + `s1.strip()`,类似于`trim()`,但是也可以移除类似中文的空格字符`\u3000`
   >   + `s1.stripLeading()`，删除头部空白字符
   >   + `s1.stripTailing()`，删除尾部空白字符
   >
   > + 判断是否空
   >
   >   + `s1.isEmpty()`:判断是否为空
   >   + `s1.isBlank()`:判断是否为空白字符
   >
   > + 替换字串
   >
   >   + `s1.repalce("l","w")`，将s1中所有的“l”字符替换为“w”
   >   + `s1.replaceAll("正则表达式","替换字符")`
   >
   > + 分割字符串
   >
   >   + `s1.split("正则表达式")`
   >
   > + 拼接字符串
   >
   >   + `String.join("分隔符",CharSequence ... )`
   >
   >     > 其内部使用了StringJoiner类，StringJoiner内部使用了StringBuilder类
   >
   > + 构造字符串
   >
   >   + `String.valueOf(所有类型)`
   >
   > + 从字符串转换
   >
   >   + `包装器类型.parseXxx("xxx")`
   >   + `Integer.getInteger(String)`：不是把字符串转化为int，而是把该字符串对应的系统变量转换为Integer
   >   + `s1.toCharArray()`，转换为char数组
   >
   > + Java的`String`和`char`在内存中总是以Unicode编码表示。
   >
   > + 早期JDK版本的String总是以char[]存储，较新的JDK则以byte[]存储：如果`String`仅包含ASCII字符，则每个`byte`存储一个字符，否则，每两个`byte`存储一个字符，这样做的目的是为了节省内存，因为大量的长度较短的`String`通常仅包含ASCII字符。

2. StringBuilder

   > 虽然可以直接用“+”拼接字符串，但是，在循环中，每次循环都会创建新的字符串对象，然后扔掉旧的字符串。这样，绝大部分字符串都是临时对象，不但浪费内存，还会影响GC效率。这是就用到了`StringBuilder`
   >
   > 为了能高效拼接字符串，Java标准库提供了`StringBuilder`，它是一个可变对象，可以预分配缓冲区，这样，往`StringBuilder`中新增字符时，不会创建新的临时对象
   >
   > `StringBuilder`还支持链式操作，即
   >
   > ```java
   > StringBuilder buider = new StringBuider();
   > buider.append("sun").append("de").append("hui");
   > ```

3. StringJoiner

   > 用于高效拼接字符串，其内部使用了`StringJoiner`类
   >
   > 用法如下
   >
   > ```java
   > String[] names = {"sun","de","hui"};
   > StringJoiner joiner = new StringJoiner(",","Hello ", "!");
   > for (String item:names){
   >     joiner.add(item);
   > }
   > joiner.toString();
   > //结果为 Hello sun,de,hui!
   > ```

4. 包装器类型

   > + 自动装箱和自动拆箱都只发生在编译阶段，目的是少写代码
   >
   > + 装箱和拆箱会影响代码的执行效率，因为编译后的class文件是严格区分基本类型和引用类型的，并且自动拆箱会导致`NullPointerException`异常
   >
   > + 所有的包装器类型都是不变类，即用`final`修饰
   >
   > + Integer内部有缓存优化，凡是-128~127之间的数字都是同一个实例
   >
   >   ```java
   >   //创建Integer实例时有以下两种方法
   >   Integer n = new Integer(100);
   >   Integer n = Integer.valueOf(100);
   >   //应该优先选用方法2，这是因为在创建对象时优先使用静态工厂方法，而不是new操作符
   >   ```
   >
   > + 整数和浮点数的包装类型都继承自`Number`
   
5. JavaBean

   > + 符合如下两个规范给的Java类
   >
   >   + 若干`private`实例字段
   >   + 通过`public`方法读写实例字段
   >
   > + 属性
   >
   >   > 一组对应的读方法和写方法称为属性
   >   >
   >   > 只有读方法成为只读属性
   >   >
   >   > 只有写方法成为只写属性
   >
   > + 作用
   >
   >   > 把一组数据组合成一个JavaBean便于传输
   >
   > + 枚举JavaBean属性,`Introspector`类的使用
   >
   >   ```java
   >   BeanInfo info = Introspector.getBeanInfo(Person.class);
   >   for(PropertyDescriptor pd:info.getProPertyDescriptors){
   >       sout(pd.getName());
   >       ...
   >   }
   >   ```
   
6. 枚举类
   
   > + 使用枚举有如下好处
   >
   >   + 枚举类中的枚举项本身带有类型信息，即Weekday.SUN的类型时Weekday
   >   + 不可能引用到非枚举的值，因为无法通过编译
   >   + 不同类型的枚举无法比较或者赋值，因为类型不符
   >
   > + `enum`类型的比较可以使用`==`，而且不推荐使用`equals()`方法,这是因为`enum`类型的每个常量在JVM中始终只有一个实例，即**理想的单例**
   >
   > + `enum`类和普通的类的区别
   >
   >   > 唯一的区别在于枚举类的实例是**有限的且定义好的**
   >   >
   >   > 其余没有区别
   >   >
   >   > 比如：
   >   >
   >   > ```java
   >   > public enum Color {
   >   >     RED, GREEN, BLUE;
   >   > }
   >   > 
   >   > //编译器编译出的class大概就像这样
   >   > public final class Color extends Enum {
   >   >     // 继承自Enum，标记为final class
   >   >     // 每个实例均为全局唯一:
   >   >     public static final Color RED = new Color();
   >   >     public static final Color GREEN = new Color();
   >   >     public static final Color BLUE = new Color();
   >   >     // private构造方法，确保外部无法调用new操作符:
   >   >     private Color() {}
   >   > }
   >   > ```
   >
   > + 枚举类的两个重要方法
   >
   >   + `name()`：返回实例名
   >
   >   + `ordinal()`：返回实例下标，从0开始
   >
   >     > 但是改变枚举常量定义的顺序就会导致`ordinal()`的结果有变化，如：
   >     >
   >     > ```java
   >     > public enum Weekday {
   >     >     SUN, MON, TUE, WED, THU, FRI, SAT;
   >     > }
   >     > public enum Weekday {
   >     >     MON, TUE, WED, THU, FRI, SAT, SUN;
   >     > }
   >     > //以上的ordinal()方法返回的结果就不一样
   >     > ```
   >     >
   >     > 所以为了保证程序的健壮性，就不能使用`ordinal`返回的结果，所以：
   >     >
   >     > ```java
   >     > enum Weekday {
   >     >     MON(1), TUE(2), WED(3), THU(4), FRI(5), SAT(6), SUN(0);
   >     > 
   >     >     public final int dayValue;
   >     > 
   >     >     private Weekday(int dayValue) {
   >     >         this.dayValue = dayValue;
   >     >     }
   >     > }
   >     > //这样就无需担心顺序的变化，新增枚举常量时，也需要指定一个int值。
   >     > ```
   >
   > + `enum`的构造方法要声明为`private`，字段强烈建议声明为`final`；
   
7. `BigInteger`和`BigDecimal`

   > + `BigInteger`内部维护了一个`int[]`数组
   > + `BigDecimal`内部使用了`BigInteger`和`scale`两个变量
   > + `BigDecimal`之间的比较用`compareTo()`方法

8. 常用工具类

   > + Math类，用于计算
   >
   > + Random类，用于生成伪随机数
   >
   > + SecureRandom类，用于生成安全随机数，真随机数使用量子力学原理获取
   >
   >   > `SecureRandom`无法指定种子，使用RNG算法，安全性是通过操作系统提供的安全的随机种子来生成随机数。这个种子是通过CPU的热噪声、读写磁盘的字节、网络流量等各种随机事件产生的“熵”。
   >   >
   >   > 在使用`SecureRandom`时应使用默认随机源，即使用无参构造，默认算法为“nativePRNG”,jdk8中如下securerandom.source=file:/dev/random
   >   >
   >   > 在Linux系统中，/dev/random时系统提供的安全随机数接口
   >   >
   >   > **概念**
   >   >
   >   > + “熵值”：随机值的**不确定性度量值**
   >   > + “熵源”：随机值的来源
   >   > + “熵输入”：伪随机数产生器描述从熵源获取到bit串，用来产生种子
   >   > + “种子”：输入到伪随机数产生器用于初始化的bit串
   >   >
   >   > **熵源不足的阻塞**
   >   >
   >   > >  有时无法满足产品对随机数的使用要求，熵源不足时存在阻塞，会导致得到随机数的速度太慢。
   >   > >
   >   > > 在读取时，/dev/random设备会返回小于熵池噪声总数的随机字节。/dev/random可生成高随机性的公钥或一次性密码本。若熵池空了，对/dev/random的读操作将会被阻塞，直到收集到了足够的环境噪声为止。
   >   > >
   >   > > **解决方案**
   >   > >
   >   > > 采用heveged守护进程增加系统熵池熵值以提高/dev/random读取随机数的速度

# 异常

```ascii
                     ┌───────────┐
                     │  Object   │
                     └───────────┘
                           ▲
                           │
                     ┌───────────┐
                     │ Throwable │
                     └───────────┘
                           ▲
                 ┌─────────┴─────────┐
                 │                   │
           ┌───────────┐       ┌───────────┐
           │   Error   │       │ Exception │
           └───────────┘       └───────────┘
                 ▲                   ▲
         ┌───────┘              ┌────┴──────────┐
         │                      │               │
┌─────────────────┐    ┌─────────────────┐┌───────────┐
│OutOfMemoryError │... │RuntimeException ││IOException│...
└─────────────────┘    └─────────────────┘└───────────┘
                                ▲
                    ┌───────────┴─────────────┐
                    │                         │
         ┌─────────────────────┐ ┌─────────────────────────┐
         │NullPointerException │ │IllegalArgumentException │...
         └─────────────────────┘ └─────────────────────────┘
```

由以上的继承关系可知，`Throwable`是异常体系的根，其下有`Error`和`Exception`两个体系：

+ `Error`表示严重的错误，程序一般对此无能为力

  + `OutOfMemoryError`：内存耗尽
  + `NoClassDefFoundError`：无法加载某个类
  + `StackOverflowError`：栈溢出

+ `Exception`是运行时错误，可以被捕获并处理，`Exception`又分为两大类

  + `RuntimeException`及其子类

    > **运行时异常的特点是Java编译器不会检查它，也就是说，当程序中可能出现这类异常，即使没有用try-catch语句捕获它，也没有用throws子句声明抛出它，也会编译通过。** 这类异常一般是由于程序逻辑错误引起的，程序应该从逻辑角度尽量避免这类异常的发生。

  + 非`RuntimeException`

    > 从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如IOException、SQLException等以及用户自定义的Exception异常，一般情况下不自定义检查异常

    + `IOException`
    + `ReflectiveOperationException`

  > Java规定：
  >
  > + 必须捕获的异常，除`RuntimeException`及其子类以外的异常
  > + 不需要捕获的异常，`Error`及其子类，`RuntimeException`及其子类

+ 异常屏蔽

  > 如果在执行`finally`语句时抛出异常，则`catch`语句的异常就无法继续抛出，这是可以预先定义一个`Exception`类型的引用,假设为`origin`，将`catch`的异常保存起来，在`finally`中调用`e.addSupressed(origin)`,这样在抛出`finally`中的异常时就可以将`catch`中的异常一起抛出

## 断言

断言是一种调试程序的方式，使用关键字`assert`来实现断言

Java断言的特点是：断言失败时会抛出`AssertionError`，导致程序结束退出。因此，断言不能用于可恢复的程序错误，只应该用于开发和测试阶段。对于可恢复的程序错误，不应该使用断言。应该抛出异常并在上层捕获

JVM在默认情况下关闭了断言指令，必须启用断言：

`$ java -ea com.daviswin.domain.Main.java`:对特定的类启用断言

`$ java -ea com.daviswin.domain...`:对特定的包启用断言

设计开发中很少用断言，都是单元测试

## 日志

使用日志记录程序的运行情况，观察每一步的结果与代码逻辑是否符合，然后有针对性地修改代码，日志在很大程度上取代了`System.out.println()`

使用日志的好处：

+ 设置输出样式
+ 设置输出级别，例如只输出错误日志
+ 可以被重定向到文件，这样可以在程序运行结束后查看日志
+ 可以按照包名控制日志级别，只输出某些包的日志

使用日志解决的问题：

+ 控制日志输出的内容和格式
+ 控制日志输出的位置
+ 日志优化：异步日志，日志文件的归档和压缩
+ 日志系统的维护
+ 面向接口开发 -- 日志的门面

日志门面：

+ JCL
+ slf4j

日志实现：

+ JUL，logback，log4j，log4j2

### JUL

位于`java.util.logging`包下

### 架构

![image-20200129230634317](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200129230634317.png)

+ Loggers: 记录器，应用程序通过获取Logger对象，调用API发布日志信息，通常为应用程序的入口
+ Handlers:也称为Appenders，每个Logger都会关联一组Handlers，Logger将日志交给关联的Handlers处理，Handlers负责将日志做记录。Handlers在此是一个抽象，其具体的实现决定了日志记录的位置可以是控制台、文件、网络上。。
+ Layouts:也成为Formatters，它负责对日志事件中的数据进行转换和格式化，它决定了一条记录在日志记录中的最终形式
+ Level:每条日志都有一个关联的日志级别，该级别粗略指导了日志消息的重要性和紧迫性，可以将Level和Loggers、Handlers做关联以便于过滤消息
+ Filters:过滤器，根据需要定制那些信息会被记录，哪些信息会被放行

> 用户使用Logger进行日志记录，Logger持有若干个Handler，Handler完成日志的输出操作，在Handler输出日志前，会经过Filter的过滤，Handler会将日志输出到指定位置，Handler在输出日志时会使用Layout，将输出内容进行排版



Java标准库内置的日志有以下局限：

> + Logging系统在JVM启动时读取配置文件并完成初始化，一旦开始运行`main()`方法，就无法修改配置
>
> + 配置不方便，需要在jvm启动时传递参数`-Djava.util.logging.config.file=<config-file-name>`

Java标准库内置的日志的配置文件（位于jdk/jre/lib/logging.properties）

```python
############################################################
#  	Default Logging Configuration File
#
# You can use a different file by specifying a filename
# with the java.util.logging.config.file system property.  
# For example java -Djava.util.logging.config.file=myfile
############################################################

############################################################
#  	Global properties
############################################################

# "handlers" specifies a comma separated list of log Handler 
# classes.  These handlers will be installed during VM startup.
# Note that these classes must be on the system classpath.
# By default we only configure a ConsoleHandler, which will only
# show messages at the INFO and above levels.
handlers= java.util.logging.ConsoleHandler

# To also add the FileHandler, use the following line instead.
#handlers= java.util.logging.FileHandler, java.util.logging.ConsoleHandler

# Default global logging level.
# This specifies which kinds of events are logged across
# all loggers.  For any given facility this global level
# can be overriden by a facility specific level
# Note that the ConsoleHandler also has a separate level
# setting to limit messages printed to the console.
.level= INFO

############################################################
# Handler specific properties.
# Describes specific configuration info for Handlers.
############################################################

# default file output is in user's home directory.
java.util.logging.FileHandler.pattern = %h/java%u.log
java.util.logging.FileHandler.limit = 50000
java.util.logging.FileHandler.count = 1
java.util.logging.FileHandler.formatter = java.util.logging.XMLFormatter

# Limit the message that are printed on the console to INFO and above.
java.util.logging.ConsoleHandler.level = INFO
java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter


```

自定义配置文件

```java
public class Test{
    @Test
    testLogProperties() throws Exception{
        //读取配置文件
        InputStream ins = Test.class
            .getClassLoader()
            .getResourceAsStream("logging.properties");
        //创建LogManager
        LogManager logManager = LogManager.getLogManager();
        //通过LogManager加载配置文件
        logManager.readConfiguration(ins);
        
        //一般log操作
    }
}
```



#### JDK Logging 的7个日志级别

+ SERVER
+ WARNING
+ INFO(默认级别，默认情况下，该级别以下的日志不会被打印)
+ CONFIG
+ FINE
+ FINER
+ FINEST

> 使用日志级别的好处在于，调整级别就可以屏蔽掉很多调试相关的日志输出

# 反射

Java的反射是指运行期拿到一个对象的所有信息

反射是为了解决在运行期，对某个实例一无所知的情况下，如何调用其方法。