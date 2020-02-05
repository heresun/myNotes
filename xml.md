# XML详解 Extensible Markup Language

## XML文件的作用

 

| 作用     | 描述                                                 |
| -------- | ---------------------------------------------------- |
| 存       | 作为特殊的文件存储数据，比数据库小，比普通文件存取快 |
| 传       | 作为网络数据传输的载体                               |
| 配置文件 | 一般用于框架的配置文件                               |

## XML文件的特点

1. 平台无关性
2. 90%以上的语言都支持
3. 自我描述性（自己定义标签）



## XML文件的语法

1. 文档声明

   `<?xml version="1.0" encoding="UTF-8"?>`

   ```xml
   
   
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
     xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
     <modelVersion>4.0.0</modelVersion>
   
     <groupId>com.heresun</groupId>
     <artifactId>learnJava</artifactId>
     <version>1.0-SNAPSHOT</version>
   
     <name>learnJava</name>
     <!-- FIXME change it to the project's website -->
     <url>http://www.example.com</url>
   
     <properties>
       <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
       <maven.compiler.source>1.7</maven.compiler.source>
       <maven.compiler.target>1.7</maven.compiler.target>
     </properties>
   
     <dependencies>
       <dependency>
         <groupId>junit</groupId>
         <artifactId>junit</artifactId>
         <version>4.11</version>
         <scope>test</scope>
       </dependency>
     </dependencies>
   
     <build>
       <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
         <plugins>
           <!-- clean lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#clean_Lifecycle -->
           <plugin>
             <artifactId>maven-clean-plugin</artifactId>
             <version>3.1.0</version>
           </plugin>
           <!-- default lifecycle, jar packaging: see https://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_jar_packaging -->
           <plugin>
             <artifactId>maven-resources-plugin</artifactId>
             <version>3.0.2</version>
           </plugin>
           <plugin>
             <artifactId>maven-compiler-plugin</artifactId>
             <version>3.8.0</version>
           </plugin>
           <plugin>
             <artifactId>maven-surefire-plugin</artifactId>
             <version>2.22.1</version>
           </plugin>
           <plugin>
             <artifactId>maven-jar-plugin</artifactId>
             <version>3.0.2</version>
           </plugin>
           <plugin>
             <artifactId>maven-install-plugin</artifactId>
             <version>2.5.2</version>
           </plugin>
           <plugin>
             <artifactId>maven-deploy-plugin</artifactId>
             <version>2.8.2</version>
           </plugin>
           <!-- site lifecycle, see https://maven.apache.org/ref/current/maven-core/lifecycles.html#site_Lifecycle -->
           <plugin>
             <artifactId>maven-site-plugin</artifactId>
             <version>3.7.1</version>
           </plugin>
           <plugin>
             <artifactId>maven-project-info-reports-plugin</artifactId>
             <version>3.0.0</version>
           </plugin>
         </plugins>
       </pluginManagement>
         <plugins>
             <plugin>
                 <groupId>org.apache.maven.plugins</groupId>
                 <artifactId>maven-compiler-plugin</artifactId>
                 <configuration>
                     <source>8</source>
                     <target>8</target>
                 </configuration>
             </plugin>
         </plugins>
     </build>
   </project>
   ```

2. 必须有根元素

3. 大小写敏感

4. 标签必须开合都有

5. 属性值必须用引号引起来（单双引号都可以）

## CDATA区

`<![CDATA[ 内容  ]]>`,

写在CDATA区中的内容忽略特殊符号

## DTD文件

document type definition:文档类型定义

```dtd
<?xml version="1.0"?>
<!DOCTYPE note [
  <!ELEMENT note (to,from,heading,body)>
  <!ELEMENT to      (#PCDATA)>
  <!ELEMENT from    (#PCDATA)>
  <!ELEMENT heading (#PCDATA)>
  <!ELEMENT body    (#PCDATA)>
]>
```

作用：为xml文件提供约束

## XSD文件

xml schemas definition：xml结构定义

它是dtd的替代品，比dtd高端

```xsd
<?xml version="1.0"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema"
targetNamespace="http://www.w3school.com.cn"
xmlns="http://www.w3school.com.cn"
elementFormDefault="qualified">

<xs:element name="note">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="to" type="xs:string"/>
        <xs:element name="from" type="xs:string"/>
        <xs:element name="heading" type="xs:string"/>
        <xs:element name="body" type="xs:string"/>
      </xs:sequence>
    </xs:complexType>
</xs:element>

</xs:schema>
```

优点：

+ xsd的语法基于xml，没有专门的语法，和xml一样解析和处理
+ 支持数据类型



# 解析XML

## 方式一：DOM解析

### 特点

+ dom在解析xml文件时根据xml文件的层次结构在内存中生成一棵树状结构
+ dom的优点是可以遍历和修改节点的内容
+ 缺点是占用内存过大

## 方式二：SAX解析

### 特点

+ 相对于DOM更快
+ 不能修改节点内容

## 方式三：JDOM解析

### 特点

+ 仅仅适用具体的类，不适用于接口，不灵活，不常用

## 方式四：DOM4J解析

### 特点

+ JDOM的智能分支，合并了许多超出基本XML文档的功能
+ 合并了以上三种方式的优点

```java
package com.heresun;

import com.sun.deploy.ui.DialogTemplate;
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import java.io.InputStream;
import java.util.List;

public class XMLTest {
    public static void main(String[] args) throws Exception {
        // 1.加载文件
        InputStream rs = XMLTest.class.getClassLoader().getResourceAsStream("test.xml");
        // 2.创建解析对象
        SAXReader saxReader = new SAXReader();
        // 3.获取文件流,并将其转换为一个文档对象
        Document read = saxReader.read(rs);
        // 4.获得根元素
        Element rootElement = read.getRootElement();

        List<Element> elements = rootElement.elements();

        for (Element element : elements) {

            System.out.println(element.getName());
        }

    }
}

```



## XPath

首先引入包

```xml
<dependency>
  <groupId>jaxen</groupId>
  <artifactId>jaxen</artifactId>
  <version>1.1.1</version>
</dependency>
```

```java
package com.heresun;

import com.sun.deploy.ui.DialogTemplate;
import org.dom4j.Document;
import org.dom4j.Element;
import org.dom4j.io.SAXReader;

import java.io.InputStream;
import java.util.List;

public class XMLTest {
    public static void main(String[] args) throws Exception {
        // 1.加载文件
        InputStream rs = XMLTest.class.getClassLoader().getResourceAsStream("test.xml");
        // 2.创建解析对象
        SAXReader saxReader = new SAXReader();
        // 3.获取文件流,并将其转换为一个文档对象
        Document read = saxReader.read(rs);
        // 4.获得根元素
        Element rootElement = read.getRootElement();
        
        //获取所有标签名为name的元素
        List<Element> elements = rootElement.selectNodes("name");
        //获取第一个标签名为name的元素
        List<Element> elements = rootElement.selectNodes("name[1]");
        //获取最后一个标签名为name的元素
        List<Element> elements = rootElement.selectNodes("name[last()]");
        //获取属性为type的name的元素
        List<Element> elements = rootElement.selectNodes("name[@type]");
        //获取属性为type的值为cc的name的元素
        List<Element> elements = rootElement.selectNodes("name[@type='cc']");
        
		//获取前两个student标签下所有标签名为name的元素
        List<Element> elements = rootElement.selectNodes("student[position()<3]/name");
        //忽略层级和位置，获取所有name标签
		List<Element> elements = rootElement.selectNodes("//name");
        //获取年龄超过22的student标签元素
        List<Element> elements = rootElement.selectNodes("student[age>22]");
    }
}

```

