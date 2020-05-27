# 1. 访问修饰符

| 修饰符    | 类内部 | 同一个包 | 子类 | 任何地方 |
| --------- | ------ | -------- | ---- | -------- |
| private   | Yes    |          |      |          |
| 缺省      | Yes    | Yes      |      |          |
| protected | Yes    | Yes      | Yes  |          |
| public    | Yes    | Yes      | Yes  | Yes      |

class 只能被 private 和 （缺省）修饰，（缺省）修饰的类只可以在包内被访问。

# 2. 构造器

+ 一旦显示定义构造器，便不提供默认构造器。
+ 构造器可以重载。
+ 父类的构造器不可以被子类继承。
+ new 对象实际上就是调用了类的构造方法

# 3. this和super

### 3.1 this

this指代当前对象，可以通过`this.属性`或`this.方法`的方式调用属性或者方法。

+ this(args); **这种代码只能写在构造函数的第一行**。

+ 构造函数不能自己调用自己；

  ```java
  class Test(){
      public Test(){
          this(); // 这样是不行的
      }
  }
  ```

+ 构造函数不能相互调用。

  ```java
  class Test(){
      private String name;
      
      public Test(){
          this("sun"); // 这样是不行的
      }
     
      public Test(String name){
          this();
      }
  }
  ```

### 3.2 super

super 是指向指向最近超类的一个指针，可以使用`super.xxx`调用父类成员，私有成员除外。

+ 子类所有的构造器都默认调用了父类的无参构造器

+ super(args) 为调用父类的某个构造函数，放在子类构造函数的第一行；

+ 如果父类没有无参构造的话，必须在子类的构造函数中使用 super 调用父类的一个有参构造。

+ this和super不能同时出现在一个构造函数里面，因为this必然会调用其它的构造函数，其它的构造函数必然也会有super语句的存在，所以在同一个构造函数里面有相同的语句，就失去了语句的意义，编译器也不会通过。
+ this()和super()都指的是对象，所以，均不可以在static环境中使用。包括：static变量,static方法，static语句块。
+ 从本质上讲，this是一个指向本对象的指针, 然而super是一个Java关键字。

# 4. 类实例化过程

![image-20200522133041547](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200522133041547.png)

![image-20200522133128715](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200522133128715.png)

# 5. Java Bean

Java Bean是遵从了一系列规范的 Java 类，是一系列可重用组件。规范如下：

+ 类是公共的
+ 属性私有
+ 每个属性都有其getter和setter方法
+ 有一个无参构造器

# 6. 多态

### 6.1 多态概念

**多态在Java中有两个重要体现**

+ 方法的重载和重写
+ 对象的多态性——可以直接应用在抽象类和接口上

**Java引用变量有两个类型：编译时类型和运行时类型：**

+ 编译时类型：由声明该变量时使用的类型决定
+ 运行时类型：由实际赋给该变量的对象决定

==若编译时类型和运行时类型不一致，则会出现多态（Polymorphism）==

1. 对象的多态性：子类的对象可以替代父类的对象使用
2. 子类可以看作是特殊的父类，所以父类类型的引用可以指向子类对象，即向上转型。

### 6.2 动态绑定

```java
class Person{
    public void showInfo(){}
}
class Student extends Person{
    public void showInfo(){}
    main(String args[]){
        Person p = new Student();
        p.showInfo;
    }
}
// p.showInfo()调用的是子类的showInfo方法
// 在编译时 p 的类型位Person，但是在方法调用是在运行时决定的，因此调用的是Student的showInfo方法，    这称为 动态绑定。
```

6.3 多态小结

出现多态的前提：

+ 需要有继承关系或者实现关系
+ 子类要重写父类方法

成员方法：

+ 编译时：查看应用变量所属的类是否有调用的方法，有则编译通过，否则编译失败
+ 运行时：调用实际对象所属类中的重写方法

成员属性：

+ 不具备多态性，只看引用变量所属的类

# 7 匿名子类

```java
public class Person{
    public static void main(String []args){
        new Person(){
            // 这里就是匿名子类，实例化最终是Person类的子类对象
            {
                // 实例代码块代替匿名子类的构造器，没名字，所以没办法像正常情况那样使用构造函数
            }
        }
    }
} // 父类
```

# 8 内部类

内部类顾名思义，即写在类内部的类：

```java
public class OutClass{
    class InnerClass{
        
    }
}// InnerClass 即为内部类
```

内部类的要求：

+ 内部类可以被 private 和 protected 修饰
+ 可以是 static，此时内部类不可以再使用外部类的非 static 成员
+ 可以是 abstract，即抽象类

==非 static 的内部类的成员变量不可以是 static  的，即只有外部类和 static 内部类中才可以声明静态变量==

**内部类可以实现Java类的多继承。**

# 9 接口与解耦

Java接口常常用于解耦操作，那么Java为什么可以解耦呢？首先明确的是接口提供的是一种标准，而实现接口的类必须遵守这个标准，否则编译不通过。这样我们在使用某个功能时，就不需要依赖具体的实现类了，只需要依赖接口就可以了。

比如：有一个磁盘接口（Disk），其有一个抽象方法：save，其有两个实现类：硬盘（HardDIsk） 和 U盘（UDisk）。

现在有一个下载类，如下：

```java
class Download{
    Disk disk;
    
    void save(File file){
        disk.save(file);
    }
    
    void setDisk(Disk disk){
        this.disk = disk;
    }
}
```

在使用时，可以根据需求，将数据存储到U盘或者硬盘中：

```java
main(String args[]){
    Download download = new Download();
    
    //存储到U盘中
    download.setDisk(new UDisk());
    download.save(file);
    
    //存储到硬盘中
    download.setDisk(new HardDisk());
    download.save(file);
}
```

由上可知，Download类只依赖了Disk接口，而不是某个具体类型的磁盘，这样在使用时，就可以择机选择，这样就将Download类和磁盘类型解耦了。

# 10 接口与抽象类

接口和抽象类的共同点是，两者都包含抽象方法，都不可以实例化，在使用时，二者的差别如下：

+ 抽象类是对一类事物的抽象，既有属性也有方法。
+ 接口是对方法的抽象，也就是对一系列动作的抽象。
+ 当需要对一类事物抽象的时候，应该使用抽象类，进而形成一个父类。
+ 当需要对一系列动作抽象的时候，要使用接口，需要使用这些动作的类实现这些接口即可。

# 11 异常

Java在运行期中的异常主要分为两大类：

+ Error: JVM系统内部错误，如：栈溢出、内存耗尽
+ Exception：其他因编程错误或偶然的外在因素导致的一般性问题，如：空指针异常、访问不存在的文件、网络连接中断

### 11.1 Java异常继承图

![image-20200527160250023](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200527160250023.png)

程序员处理的异常主要为`RuntimeException`，程序员没有能力处理`Error`

### 11.2 Java异常处理模式

+ Java程序的执行过程如果出现异常，会自动生成一个异常对象，将该对象提交给Java运行时系统，这一过程称为异常抛出
+ 程序员通过`try...catch`语句将程序抛出的异常处理，这一过程称为异常的捕获。

如果一个异常直到main方法也没有被捕获，那么程序执行将终止。

==在捕获异常的代码块中，如果有多个catch，如果前面的catch已经捕获异常，那么后面的catch将不会被执行==

```java
try{
    
}catch(Exception e1){ // 如果e1已经被捕获，即便出现了e2的异常，也不会捕获
    
}catch(Exception e2){}
```

### 11.3 捕获异常信息

+ getMessage(), 用来得到有关异常事件的信息。
+ printStackTrace(), 追踪发生异常时的堆栈信息

### 11.4 抛出异常

#### 11.4.1 throw

`throw new Exception();`可以主动抛出异常，这一代码段是写在方法中的，同时方法也需要使用`throws`抛出同样的异常

但是 `throws` 抛出的异常范围不能小于 `throw` 抛出的异常范围。

如果 `throw` 抛出的时错误，那么方法可以不使用 `throws` 抛出

#### 11.4.2 throws

`throws` 用于方法抛出异常，如果子类重写了该方法，那么子类重写的方法所抛出的异常范围不能大于被重写的方法所抛出的异常。

# 10 单例模式

设计模式：是在大量的实践中总结和理论化之后优选的代码结构、编程风格以及解决问题的思考方式。就如同棋谱一样，不同的棋局使用不同的棋谱，免去了再思考和摸索的过程。单例模式：在整个软件系统中，对某个类只能存在一个实例对象，并且该类只提供一个取得该实例的方法。

### 10.1 单例模式的实践

首先：将类构造方法的访问权限设置位 `private`, 保证不能在外部使用 `new` 创建对象

再者：调用类提供的静态方法，获取在该类内部创建的唯一实例。

注意：静态方法只能访问静态成员变量，指向该唯一实例的变量**必须是静态类型**

#### 10.1.1 饿汉式

即当类加载初始化时就创建该类的唯一对象。

==线程安全==

#### 10.1.2 懒汉式

当第一次访问获取该类的单例对象的方法时才创建该对象。

==线程不安全，需要用其他手段保证线程安全==

### 10.2 单例模式最佳实践

使用枚举是单例模式的最佳实践，因为枚举的每个成员天生就是单例的，同时还是懒加载的。

因此这种方法同时满足了 饿汉式 的线程安全和 懒汉式 的懒加载。

```java
public class User {
    //私有化构造函数
    private User(){ }
    //定义一个静态枚举类
    static enum SingletonEnum{
        //创建一个枚举对象，该对象天生为单例
        INSTANCE;
        private User user;
        //私有化枚举的构造函数
        private SingletonEnum(){
            user=new User();
        }
        public User getInstnce(){
            return user;
        }
    }
 
    //对外暴露一个获取User对象的静态方法
    public static User getInstance(){
        return SingletonEnum.INSTANCE.getInstnce();
    }
}
```

# 11 模板方法设计模式

抽象类体现的是一种模板模式的设计，抽象类作为多个子类的通用模板，子类在抽象类的基础上进行扩展、改造，但子类总体上会保留抽象类的行为方式。

解决的问题：

+ 一部分功能确定，另外一部功能不确定。这时可以把不确定的方法设置为抽象方法，交给子类去实现。
+ 编写一个抽象父类，父类提供了多个子类的通用方法，并把一个或多个方法留给子类去实现，就是一种模板模式。

# 12 工厂模式

==在面向对象的编程中，对象的创建十分简单，但是对象创建的时机很重要。==

工厂模式解决的就是这个问题，它通过面向对象的手法，将所要创建的具体对象的创建工作延迟到了子类，从而提供了一种扩展的策略，较好地解决了这种紧耦合的关系。或者说对象的创建工作交给了工厂类完成，调用者只需要调用工厂类提供的生成对象的方法即可。