# DOM

DOM即document object model，文档对象模型

### 获取子节点

```html
    <ul id="container">
        <li></li>
        <li></li>
        <li></li>
    </ul>
```

```javascript
let oUl = document.getElementById("container");
oUl.childNodes // 返回的是一个子节点数组， 包含文本节点
oUl.children   //返回的是一个子节点数组， 不包含文本节点，仅仅只包含元素节点
```

==当前节点的子节点只计算该节点下第一层的节点==

推荐使用**children**

### 获取父节点

+ `oUl.parentNode`: 获取当前节点的父节点，该父节点唯一不变的
+ `oUl.offsetNode`: 获取当前节点的定位父节点，该父节点不一定是上面那种方式获取到的父节点，这种方法获取到的父节点是距离当前节点最近的并且设置定位的父节点

### 获取首尾节点

+ 获取首节点
  + `oUl.firstChild`: 获取到的节点是一个文本节点（**低版本浏览器不存在该问题**），nodeType为3，其性质类似于`oUl.childNodes`
  + `oUl.firstElementChild`: 获取到的节点是第一个元素节点，nodeType为1，其性质类似`oUl.children`，**低版本浏览器不支持**
+ 获取尾节点
  + `oUl.lastChild`: 问题同首节点
  + `oUl.lastElementChild`：问题同首节点

### 获取兄弟节点

==同获取首位节点问题==

+ 获取下一个兄弟节点
  + `nextSibling`：可能会获取到文本节点，**高版本存在此问题，低版本不存在**
  + `nextElementSibling`：只能获取元素节点，**低版本不支持**
+ 获取前一个兄弟节点
  + `previousSibling`
  + `priviousElementSibling`

### 获取节点类型

```javascript
oUl.nodeType
```

+ 1 表示当前节点是一个元素节点；
+ 3 表示当前节点是一个文本节点

### DOM方式操作元素的属性

+ 获取属性值：`getAttribute('属性名')`
+ 设置属性值：`setAttribute('属性名','值')`
+ 移除某属性：`removeAttribute('属性名')`

### DOM元素的创建、插入和删除

+ 创建一个dom节点

  ```javascript
  let oUl = document.getElementById("container");
  let oLi = document.createElement("li");
  ```

+ 插入一个dom节点

  ```javascript
  oUl.appendChild(oLi) // 在ul中追加一个li
  oUl.insertBefore(oLi, preOLi) // 插入preOLi之前
  ```

+ 删除一个dom节点

  ```javascript
  oUl.removeChild(oLi) // 移除一个li
  ```

### 文档碎片

理论上，文档碎片可以提高DOM操作性能

它常用于一次性插入多个元素，在创建元素时，把创建好的元素放入文档碎片中，创建完毕后，一次性渲染，避免了创建一次渲染一次的情况

```javascript
let frag = document.createDocumentFragment();
for(let i=0; i<10000; i++){
    let oLi = document.createElement("li");
    frag.appendChild(oLi);  // 把创建好的元素放入文档碎片中
}

oUl.appendChild(frag);// 把文档碎片中的元素一次性渲染出来
```

==在高级浏览器中，使用和不使用文档碎片性能基本无差别，基本不用==



# 事件

### 事件对象：`event`

```javascript
document.onclick = function(){
    alert(event.clientX) // 获取鼠标横坐标
}

// 也可以
document.onclick = function(ev){
    alert(ev.clieventX) // 获取鼠标横坐标
}
// 为了兼容
document.onclick = function(ev){
    let oEvent = ev||event;
    alert(oEvent.clientX) // 获取鼠标横坐标
}
```

### 事件流

所谓事件流即事件的传播, **或者称为事件冒泡**

事件将会逐级向父元素传递；

#### 取消事件冒泡

```javascript
event.cancelBubble = true; // 在事件函数中最后加上该行代码即可
```

### 常用事件

+ 鼠标事件

  + onclick : 点击事件
  + onmouseover : 鼠标移入事件
  + onmouseout :鼠标移出事件
  + onmousemove : 鼠标移动事件
  + onmousedown : 鼠标按下
  + onmouseup : 鼠标抬起

+ 键盘事件

  + onkeydown : 键盘按键按下事件

    + event.keyCode : 从event对象中获取按下的按键的 “ 键值 ”

    + **event.ctrlKey :  判断是否是ctrl键**

    + **event.shiftKey : 判断是否是shift键**

    + **event.altKey : 判断是否是alt键**

      > 黑体字都是提供的常用按键的快捷方式

  + onkeyup : 键盘按键抬起事件

+ window对象常用事件

  + onscroll : 当滚动条滚动时发生的事件
  + onresize : 当窗口大小变化触发
  + onload : 当页面加载完成触发

+ input框常用事件：
  + onchange : 当input框内容发生变化时触发事件








### 默认行为

即浏览器自带的事件，如右击鼠标，弹出菜单。

#### 阻止默认行为

```javascript
document.oncontextmenu = function(){
    return false; // 阻止默认事件，此时再点击鼠标右键，将不会弹出系统菜单
}
// 这里的oncontextmenu可以是任意事件，如onclick、onchange、onkeydown、onkeyup、onmouseover、onmouseout、onmousemove...

```

==如果要加一个全局的事件，将该事件加在`document`对象上==

### 事件绑定

+ 低版本IE浏览器：`attachEvent`

  ```javascript
  btn.attachEvent("onclick",function(){
      alert("a");
  })
  ```

+ 高版本IE或者现代浏览器：`addEventListener`

  ```javascript
  btn.addEventListener("click",function(){ // 注意这里的事件名没有'on'
      alert("a");
  },false)// false作用不大
  ```

### 删除事件绑定

+ 低版本IE浏览器：`detachEvent`

  ```javascript
  btn.detachEvent("onclick",function(){
      alert("a");
  })
  ```

+ 高版本IE或者现代浏览器：`removeEventListener`

  ```javascript
  btn.removeEventListener("click",function(){ // 注意这里的事件名没有'on'
      alert("a");
  },false)// false作用不大
  ```

==如果绑定的是一个匿名函数，将无法删除==

# ajax

在不刷新页面的情况下与服务器进行数据交互。

1. 创建AJax对象

   ```javascript
   let ajax = new XMLHttpRequest(); // 非IE6浏览器兼容
   let ajax = new activeXObject('Microsoft.XMLHTTP') // IE6兼容
   ```

2. 连接到服务器

   ```javascript
   // ajax.open(方法，资源路径，是否异步)
   ajax.open("GET", "http://localhost:8080/elive/house/getPage", true)
   ```

3. 发送请求

   ```javascript
   ajax.send()
   ```

4. 接收返回值

   ```javascript
   ajax.onreadystatechange = function(){
       if(ajax.readyState == 4){
           if(ajax.status == 200){ // 成功
               alert("成功"+ajax.responseText); // responseText是响应文本内同
           }else{
               alert("失败");
           }
       }
   }
   ```

   + **readyState**的值所代表的意义
     + 0 未初始化，还未调用**open**方法
     + 1 载入，已调用**send()**方法，正在发送请求
     + 2 载入完成，**send()**方法完成，已收到所有响应内容，这时的数据可能是加密或者压缩过的，不能直接使用
     + 3 解析，正在解析响应内容
     + 4 完成，响应内容解析完成，可以在客户端调用, 注意，无论是否请求成功，都会跳到4



# 面向对象

```javascript
// 1 创建对象构造函数
function CreatePerson(name, age){
    this.name = name;
    this.age = age;
}

// 2 为构造函数原型添加方法，这些方法是公用的
CreatePerson.prototype.showName = function(){
    alert(this.name)
}
CreatePerson.prototype.showAge = function(){
    alert(this.age)
}

// 3 创建对象
let obj = new CreatePerson("孙德辉",12); // 这里重新创建一个Objct，上面中this指这个Object

// 4 调用方法
obj.showName()
```



# BOM的简单使用

**window是BOM的体现**

#### window对象的属性

+ window.document : 即document对象
+ window.open("url","\_self/\_blank") : 打开url指向的网址, \_self和\_blank为打开新网页的方式
+ window.close() : 关闭当前网页
+ window.location : 返回地址栏的url
+ window.documentElement.clientWidth : 可视区宽度
+ window.documentElement.clientHeight : 可视区高度
+ window.body.scrollTop : 
+ window.documentElement.scrollTop : 可视区顶端距离滚动顶端的大小， 兼容IE和火狐
+ window.body.scrollTop : 同上，建通Chrome
+ window.navicator.userAgent : 浏览器类型

#### 系统对话框

+ 警告框：alert("内容")，无返回值
+ 确认框：confirm("提问内容")，返回布尔值
+ 输入框：prompt("提示"，"默认内容(可选)"), 返回输入的内容



# COOKIE

cookie是用来保存页面信息的，同一个域名下的网页共享同一套cookie。cookie的数量和大小是有限制的，一般为4-10k，并且会过期,cookie的默认过期时间是浏览器关闭。

### 在js中使用cookie

在js中，cookie是document对象的一个属性，即document.cookie

#### 在cookie中存内容：

cookie存储的内容是**key=value**，document.cookie = "username=heresun"

#### 设置cookie的过期时间

```javascript
let date = new Date();
date.setDate(date.getDate()+14); // 将cookie设置为两周后过期
document.cookie = "username=heresun;expires="+date
```

#### 操作cookie

```javascript
// 存cookie
function setCookie(name,value,timeout){
    var oDate = new Date();
    oDate.setDate(oDate.getDate()+timeout);
    
    document.cookie = name+"="+value+";expires="+oDate;
}

// 取cookie, cookie存储的格式是 key1=val1;key2=val2;key3=val3,并且是一个字符串
function getCookie(name){
    let cookies = document.cookie.split(";");
    for(let i=0; i<cookies.length; i++ ){
        let cookie = cookies[i].split("=");
        if(name === cookie[0]){
            return cookie[1];
        }
    }
    // 如果没有查找到，返回一个空字符串
    return "";
}

// 删除一个cookie
function removeCookie(name){
    setCookie(name,1,-1);// 将过期时间设置为过去的某个时间，value无所谓
}
```

# 正则表达式

### 字符串基本操作

+ search(target)，返回要查找的字符在原字符串中的下标，找不到则返回-1，它的参数可以是字符串也可以是正则表达式
+ substring(start, end),  返回截取的字符串，不包括下标为end的字符，end是可选参数
+ charAt(index), 返回下标为index的字符
+ split(分隔符), 使用分隔符分割字符串，该分隔符必须在原字符串中

### 正则

使用js的正则必须通过`RegExp`对象；

```javascript
let pattern = new RegExp('a',"i"); //js风格, 第一个参数是正则表达式，第二个参数是选项，i表示忽略										大小写
let pattern = /a/i;                //perl风格， i表示忽略大小写
```

字符：

+ **.**   : 匹配任意字符  

+ **\d** : 匹配数字                                            [0-9]
+ **\D** : 匹配非数字                                        [\^0-9]
+ **\w** : 匹配字母、数字、_                           [0-9_]
+ **\W** : 匹配非字母、非数字、非_               [\^0-9_]
+ **\s** : 匹配空白字符
+ **\S** : 匹配非空白字符

量词：即数量

+ {n} : 正好匹配n个
+ {n,m} : 最少匹配n个，最多匹配m个
+ {n,} : 最少匹配n个，最多不限
+ \+：一次或多次， 等价于 {1,}
+ \* : 0或多次  , 等价于 {0,}
+ ? : 0或1次， 等价于 {0,1}

位置限定符：

+ \^ : 代表行首
+ $ : 代表行尾



### 与正则相关的方法

```javascript
// 1 match方法,返回匹配到的字符数组
str.match(re);
// 2 replace方法，返回替代后的字符串，不改变原字符串
str.replace(re,newStr);
// 3 test方法，返回布尔值，如果该str符合re的规则，返回true，否则返回false。注意：test方法仅仅匹配str的部分，也就是说str只要有一部分符合re就可以通过检测，因此，如果要检测整个str，需要对re的首位进行标注，即用“^”标注开头，用"$"标注结尾
re.test(str)
```



# ES6新特性

### 变量

#### **不可重复声明，支持常量，支持块级作用域**

```javascript
let 声明的变量是作用于当前代码块中，并且不可以重复声明
const 声明的变量是作用于当前代码块中，并且不可以重复声明和重新赋值
```

#### 解构赋值

前提：

1. 左右两边必须结构一样, 但是数量可以不一致
2. 左侧是变量集合，右侧是一个对象
3. 变量的声明和赋值必须在一条代码中完成

```javascript
// 数量一致
let [a,b,c] = [1,2,3]            // a为1，b为2，c为3
let {a,b,c} = {a:0, b:10, c:78}  // a为0，b为10，c为78

// 数量不一致
let [a,b] = [1,2,3]            // a为1，b为2
let [a,b,c,d] = [1,2,3]        //  a为1，b为2，c为3，d为undefined
```



### 函数

#### 箭头函数

```javascript
// ES6之前
let func = function () {}
// ES6
let func = ()=>{}
```

#### 参数扩展

```javascript
let func = function(arg1,arg2,...args){} // args是一个数组，可变形参列表必须放在参数列表的最后
```

`...`也可以用来展开一个数组：

```javascript
let arr = [1,2,3]
let arr_2 = [...arr] // 等价于arr_2=[1,2,3]
```

#### 默认参数

```javascript
let func = function(a,b=9,c=2){} // 为参数b和c设置了默认参数
```

### 数组

#### map : 映射

将一个数组映射为另一个数组

```javascript
let arr = [2,42,90]
let result = arr.map(item=>{ // 返回值是映射后的数组
    return item*2;
})
```

#### reduce : 汇总

将数组的内容汇总为一个结果,如算总数、平均数

```javascript
let arr = [2,42,90]
// 求和
let result = arr.reduce((temp,item,index)=>{ // 返回值是汇总后的结果
    return temp+item;
})
// 求平均
let result = arr.reduce((temp,item,index)=>{ // 返回值是汇总后的结果
    if(index !== arr.length-1){//如果不是最后一个
        return temp+item;
    }else{
        return (temp+item)/arr.length;
    }
})
```

#### filter ：过滤

获取符合条件的数据

```javascript
let arr = [2,42,90]

let result = arr.filter((item,index)=>{ // 返回结果是一个数组,index可以忽略
    return item>2;
})
```

#### forEach : 循环

```javascript
let arr = [2,42,90]

arr.forEach((item,index)=>{ // 无返回值
    //处理
})
```

### 字符串

#### 新增方法

+ startsWith
+ endsWith

#### 字符串模板

简化了传统字符串拼接的方式

```javascript
let name = "heresun"
let template = `我叫做${name}` // template 为 我叫做heresun
```



