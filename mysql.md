

# 注意事项

1. 在mysql中字符串的下标是从1开始的

2. 数据类型

   1. 数值型

      + 整数 (tinyint,smallint, mediumint, int, bigint)
      + 小数
        + 定点数 (float, double)
        + 浮点数 
          + decimal(M,D): M默认为10， D默认为0 **用于精度要求高的字段**

   2. 字符型

      + 较短文本：

        + char(M)            0~255
        + varchar(M)       0~65535

        > M表示字符个数，一个字母或者汉字都视为一个字符

      + 较长文本

        + text
        + blob

      + enum枚举

      + set集合

   3. 日期型

      + date
      + datetime
      + timestamp（使用较多，它更能反映当前时区的信息，仅占4个字节）
      + time
      + year



# 常见约束

+ NOT NULL
+ UNIQUE
+ DEFAULT
+ CHECK   <font style="color:red">(mysql不支持)</font>
+ PRIMARY KEY
+ FOREIGN KEY

#### UNIQUE 和 PRIMARY KEY的区别

区别

+ 一个表只能有一个主键，但是可以有多个唯一
+ 主键不允许为空，唯一可以为空

相同

+ 都是唯一的
+ 都支持组合键，但是不推荐

# 连接查询

即查询涉及到多个表,通过查询条件将多个表连接到一起

```mysql
-- 连接查询语法
select 查询列表
from 表1
full|inner|left|right|cross  -- 连接类型
join 表2
on 连接条件
where 筛选条件
group by 分组
having 筛选条件
order by 排序列表;
```



## 内连接

#### 等值连接

```mysql
-- 查询员工及其所在部门的部门名
select name, department_name
from employees,department
where employees.department_id = department.id;

select name, department_name
from employees
inner 
join department
on employees.department_id = department.id;
```

#### 非等值连接

```mysql
-- 查询员工工资及其工资级别
select salary , grade_level
from employees,job_grades
where salary between job_grades.low_sal and job_grades.high_sal;
```

#### 自连接

```mysql
-- 查询员工名及其上级编号
select e1.name, e1.manage_id
from employees e1, employees e2
where e1.manage_id = e2.id;
```





## 外连接

应用场景：用于查询一个表中有另一个表中没有的记录

特点：

1. 外连接查询结果为主表中的所有记录，如果从表有与其匹配的显示值，否则显示null

2. 左外连接：left join 左边的是主表

   右外连接：right join右边的是主表

3. 左外连接和右外连接主从表交换顺序结果不变

4. 全外连接 = 内连接 + 左外连接 + 右外连接

```mysql
-- 连接查询语法
select 查询列表
from 表1
full|left|right  -- 连接类型
join 表2
on 连接条件
where 筛选条件
group by 分组
having 筛选条件
order by 排序列表;
```

#### 左外连接

#### 右外连接

#### 全外连接（mysql中不支持）

## 交叉连接

使用sql99语法实现的笛卡尔乘积

# 分组查询

分组查询是使用了`group by`的查询

```mysql
select 分组函数, 字段1, 字段2,...
from 表
where 分组前筛选  -- 筛选的字段是表中原有的字段
group by 分组字段 -- 该字段可以是原表中的字段，也可以是使用单行函数，
				 -- 分组字段可以有多个，将这些字段相等的分为一组，且这些字段无序
having 分组后筛选 -- 筛选的字段是分组函数的结果，分组函数做筛选条件一定放在having后
order by 排序字段; -- 该字段可以是原表字段，也可以是分组函数或者单行函数结果
```

举例

```mysql
-- 查询奖金不为空，且按姓名长度分组后，每一组的个数大于5的员工个数和醒目长度
select count(*) c, length(name) name_len
from employees
where bonus is not null
group by name_len
having c > 5;
```

==和分组查询一同出现的字段，要求是group by后出现的字段==

# 模糊查询

查询的条件是一个范围

## 关键字：

+ like

  ```mysql
  -- % 代表任意个任意字符,不区分大小写
  select * from user where username like "%孙%";
  -- _ 表示任意单个字符
  select * from user where username like "_孙_";
  
  -- 可以使用转义字符将上面的通配符转义，也可以使用escape关键字转义,如
  -- 查询用户名中第一个字符为下划线的用户名，注意$用来标记被转义的字符
  select * from user where username like "$_%" escape '$'
  ```

+ between A and B   ==包含临界值==

  ```mysql
  -- 查询年龄在18 到 28之间的用户，它包含了临界值
  -- 两个临界值不要调换位置
  select * from user where age between 18 and 28;
  ```

+ in

  ```mysql
  -- 查询用户的部门id为1，56 或 90的用户，如果in中有字符串，字符串中不支持通配符
  select * from user where dep_id in (1,56,90);
  ```

+ is null ， is not null

  ```mysql
  -- 由于=不能判断某个字段是否为null，如果判断某个字段是否为null，必须使用is null或is not null
  ```

  + 安全等于：`<=>`

    ```mysql
    -- <=> 可以判断某个字段是否为null，除此外它还可以提供和 = 一样的功能
    ```

    

# 常见函数

#### rand()

返回一个0~1的随机小数

## 单行函数

特点：传入一个参数返回一个值

### 字符函数

#### 1. LENGTH(str)

获取字符串的字节个数，不同的编码长度不同

```mysql
select length(name) from user;
```

#### 2. CONCAT(str1,str2,...)

链接字符串

```mysql
select concat(first_name,"-",last_name) from user; -- 用-把名字连接起来
```

#### 3. LOWER(str) ,UPPER(str)

将字字母字符串变为小写或大写

```mysql
select lower(name) from user;
```

#### 4. substr(str,start,[len)

截取字符串，start表示从哪一个位置（包括该位置的字符）向后截取，len表示截取的长度，如果不指明则默认到最后

```mysql
select substr(username,1,5) from user; -- 将用户名从下标1开始，截取5个字符
```

#### 5. instr(str,substr)

返回substr在str中第一次出现的位置，如果没有返会0

```mysql
select instr("今天是个好日子！","是个"); -- 结果为3
```

#### 6. trim(str)

去除str首尾的字符,如果不指定则默认去除空白字符

```mysql
select trim("a" from "aaaaaaaaaaa孙aaaa德辉aaaaaa"); -- 结果为 孙aaaa德辉
```

#### 7. replace(str, fromStr, toStr)

把str中的fromStr替换为toStr

```mysql
-- 将a替换为空白字符
select replace("aaa孙aaaa德辉aaa","a",""); -- 结果为 孙德辉
```

#### 8. lpad(str, width, strL),rpad(str,width,strR)

lpad: 如果str的宽度不足width，则在str的左边填充strL，如果str大于width，从右侧截断str

rpad: 同上面相反

```mysql
select lpad("sundehui",10,"*"); -- 结果为 **sundehui
```



### 数学函数

#### 1. round(num, [D)

对num四舍五入, D可选，表示小数点后保留几位

#### 2. ceil(num)，floor(num)

向上取整, 向下取整

#### 3. truncate(num,D)

将num从小数点后第D位截断（不是四舍五入），其后的舍弃

#### 4. mod(num1, num2)

等价于num%num2，等价于==num1-num1/num2*num2==

### 日期函数

#### 1. now()

获取当前系统的日期时间

#### 2. curdate()

获取当前系统的日期

#### 3. curtime()

获取当前系统的时间

#### 4. 获取年月日时分秒

+ 年：YEAR(日期类型)
+ 月：MONTH(日期类型)， MONTHNAME(日期类型)
+ 日：DAY(日期类型)
+ HOUR、MINUTE、SECOND

#### 5.str_to_date(str, format)

将str按照format的格式解析，解析结果为date类型

```mysql
SELECT STR_TO_DATE("1-5-2020","%d-%m-%y"); -- 结果为 2020-05-01
```

<img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200319221157612.png" alt="image-20200319221157612" style="zoom:50%;" />

#### 6. date_format(date, format)

将date类型按照format格式化，转为为字符

```mysql
SELECT DATE_FORMAT(NOW(),"%Y-%m-%d %H:%i:%s");-- 结果为 2020-03-19 22:14:20
```



### 补充

#### 1. VERSION()

```mysql
select version(); -- 查询数据的版本号
```

#### 2 DATABASE()  DATABASES()

```mysql
select database(); -- 查询当前数据库
select databases(); -- 查询所有数据库
```

#### 3. USER()

```mysql
select USER(); -- 查询当前用户
```

#### 4<span name="mark"> 流程控制函数</span>

+ IF(条件, 为真结果, 为假结果)

  相当于三元运算符

  ```mysql
  select if(10>5, "大", "小"); -- 结果为 大
  ```

+ case

  ```mysql
  -- ---------------------------case实现switch的功能
  -- 查询员工工资，要求
  -- 部门号=30，显示工资为原来的1.1倍
  -- 部门号=40，显示工资为原来的1.2倍
  -- 部门号=50，显示工资为原来的1.3倍
  -- 其他部门，显示原工资
  select salary 原工资, department_id,
  case department_id
  when 30 then salary*1.1
  when 40 then salary*1.2
  when 50 then salary*1.3
  else salary
  end as 新工资
  from employees;
  
  -- ---------------------------case实现多重if-else的功能
  -- 查询员工工资，要求
  -- 工资>2000，显示A级别
  -- 工资>3000，显示B级别
  -- 工资>5000，显示C级别
  -- 否则，显示D级别
  select salary
  case
  when salary>2000 then "A"
  when salary>3000 then "B"
  when salary>5000 then "C"
  else "D" 
  end as "工资级别"
  from employees;
  ```

  

## 分组函数

特点：传入一组参数返回一个值，**通常用来做统计，所以又称为统计函数、聚合函数、组函数**

==分组函数的参数可以用**distinct**修饰==

#### 1.sum  (一组数值)

求和，忽略null

#### 2. avg（一组数值）

求平均，忽略null

#### 3. max（数值、字符、日期）

求最大值，忽略null

#### 4. min（数值、字符、日期）

求最小值，忽略null

#### 5. count（某个字段）

计算非null的个数，忽略null

```mysql
select count(salary) from user; -- 如果salary中有null的，会导致统计行数比实际少
select count(*) from user;-- 统计user表中的行数 
select count(1) from user;-- 同上，这个在表中加了个字段，字段名为1，字段值为1 
-- myisam conunt(*)效率高
-- innodb count(*)和count(1)效率几乎一样
-- count(字段)效率最低，因为它要判断字段值是否为null
```



# 事务

一个或一组sql语句组成一个执行单元,要么都执行,要么都不执行.

一个事务中的每一条sql语句都是相互依赖的,一旦有一个执行失败,事务将回滚,回到最初状态

==MySQL所支持的存储引擎中,InnoDB支持事务==

**事务仅支持 增删改**

## 事务的ACID

| 性质                | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| 原子性(Atomicity)   | 事务的原子性表明事务是不可分割的工作单位,即要么都发生, 要么都不发生 |
| 一致性(Consistency) | 事务必须使数据库从一个**一致性状态**转换到另一个一致性状态   |
| 隔离性(Isolation)   | 一个事务的执行不被其他事务干扰, 即一个事务内部的操作及使用的数据对其他事务是隔离的, 并发执行的各个事务之间不能相互干扰 |
| 持久性(Durability)  | 一个事务一旦被提交,其对数据库的影响就是**永久性的**, 接下来数据库的其他故障对其不应该有影响 |

## 事务的创建

### 隐式事务

事务没有明显的开启和关闭标记,如update,delete,insert

### 显式事务

事务有明显的开启和关闭标记

==使用显式事务的前提是:关闭自动提交==

查看当前是否为自动提交: `show variables like "autocommit";`

关闭自动提交:`set autocommit=0;`

**创建一个事务:**

```mysql
-- 步骤 1:开启事务
    set autommit=0;    -- 关闭自动提交
    start transaction; -- 可选
-- 步骤 2:编写事务中的sql语句(增删改查),将数据存储到内存,遇到结束事务标记时再决定是回滚还是写入磁盘
	语句1;
	语句2;
	...
-- 步骤 3:结束事务
	commit;            -- 执行成功则提交事务
	rollback;          -- 执行失败则回滚事务
```



## 事务的隔离级别

如果多个事务同时访问数据库中的相同数据, 如果没有采取必要的隔离机制, 会导致如下问题:

 假设有事务T1和事务T2

| 问题       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| 脏读       | T1读取了已经被T2更新但还未被T2提交的记录, 如果T2回滚, 则T1读取的数据是临时且无效的 |
| 不可重复读 | T1读取了一条记录, T2更改了这条记录, T1再次读取这条记录, 结果不同 |
| 幻读       | T1读取了一个表中的数据, 然后T2增加或删除了几条记录,T1再读时发现多出或少了几行记录 |

为了解决以上的问题, 需要设置事务的隔离级别;

### 设置隔离级别

==隔离级别即一个事务与其他事务隔离的程度, 数据库定义了许多隔离级别, 隔离级别越高, 数据一致性越强,但是并发能力越弱.==

Mysql支持以下四种隔离级别:

| 隔离级别                        | 说明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| 读未提交数据( READ UNCOMMITTED) | 允许数据读取已被其他事务更改但还未提交的数据, **脏读\幻读\不可重复读的问题都会出现** |
| 读已提交数据(READ COMMITTED)    | 只允许事务读取已被其他事务提交的变更, **可避免脏读**         |
| 可重复读(REPEATABLE READ)       | 确保事务可以多次从一条记录中读取相同的值, 在这个事务执行期间, 禁止其他事务对这条记录进行更新, **可避免脏读和不可重复读** |
| 串行化(SERIALIZABLE)            | 确保一个事务从一个表中读取相同的行, 在这个事务执行期间, 禁止其他事务对该表执行插入 ,更新和删除操作, **避免了所有问题, 但是性能低** |

==Mysql默认的隔离级别是REPEATABLE READ==

查看MySQL的隔离级别:`select @@tx_isolation;`

设置MySQL的隔离级别:`set session transaction isolation level  read uncommitted;`

## 回滚点/保存点

```mysql
-- 步骤 1:开启事务
    set autommit=0;    -- 关闭自动提交
    start transaction; -- 可选
-- 步骤 2:编写事务中的sql语句(增删改查),将数据存储到内存,遇到结束事务标记时再决定是回滚还是写入磁盘
	语句1;
	savepoint a; -- 设置一个回滚点, a为该点的名字
	语句2;
	...
-- 步骤 3:结束事务
	rollback to a;          -- 回滚到保存点a, a之后的语句全部回滚,a之前的语句执行成功
```



# 视图

视图是一种虚拟表, 是通过实体表动态生成的, 它的行和列的数据来自于创建该视图的表, 并且是在使用视图时动态生成的, ==视图只保存了sql逻辑, 不保存查询结果==,其应用场景为:

+ 多个地方用到相同的查询结果
+ 该查询结果使用到的查询语句较复杂

优点:

+ 实现了sql语句的重用
+ 简化了sql语句, 不必知道其实现细节
+ 保护数据, 提高安全性, 对原始表的数据进行了隐藏

## 视图的创建

```mysql
-- 创建视图
-- create view 视图名
-- as 查询语句
create (or replace) view my_view
as
select stuname, majorname
from stuinfo s
inner join major m on s.major_id = m.id;
-- 使用视图
select * from my_view where stuname like "孙%";

-- 以上等价于如下代码
select * from stuinfo s
inner join major m on s.major_id = m.id
where s.stuname like "孙%";
```

==使用视图时就把它当作一个表==

## 视图的修改

### 方式一:

```mysql
create or replace view my_view -- 没有就创建该视图,有就修改
as 新的mysql语句
```

### 方式二:

```mysql
alter view my_view
as 新的mysql语句
```



## 删除视图

```mysql
drop view 视图1, 视图2 ,...;
```

## 查看视图

```mysql
-- 方法一
	desc my_veiw;
-- 方法二
	show create view my_view;
```

## 更新视图

视图最好只读,不要修改,但是可以修改

只有简单列,即视图中的列和原始表中的列一样, 可以像更新一个表一样更新视图,并且对原始表产生同样的影响

![image-20200303222224024](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200303222224024.png)

## 视图和表的对比

|      | 创建语法不一样 | 是否占用磁盘空间 | 是否可以更新 |
| ---- | -------------- | ---------------- | ------------ |
| 视图 | create view    | 否               | 否           |
| 表   | create table   | 是               | 是           |



# 流程控制结构

三大控制结构

1. 顺序结构
2. 分支结构
3. 循环结构

if函数和case函数见 <a href="#mark"> 常见函数>单行函数>补充>流程控制函数</a>

## if 结构

if结构只能用字啊存储过程或者函数中

#### 语法

```mysql
if 条件1 and 条件2 then 语句1;
elseif 条件2 then 语句2;
...
else 语句n;
end if;
```

## 循环结构

#### while

```mysql
【标签:】 while 循环条件 do  -- 写标签可以搭配循环控制使用
			循环体;
		end while; 【标签】
```

#### loop

```mysql
【标签:】 loop
			循环体;
		end loop; 【标签】
```

>  没有循环结束条件，可以用来模拟死循环

#### repeat

```mysql
【标签:】 repeat  -- 写标签可以搭配循环控制使用
			循环体;
		until 结束循环条件
		end repeat; 【标签】
```



### 循环控制

#### iterate

类似于continue, 结束本次循环，开始下一次循环

```mysql
continue 循环标签
```

#### leave

类似于breake， 结束循环

```mysql
leave 循环标签
```



# 存储过程和函数

类似于方法

+ 提高代码重用性
+ 简化操作
+ 提高了效率

## 变量

mysql中的变量分为以下几类

+ 系统变量 （由系统提供，属于服务器层面）

  + 全局变量（作用域：Mysql服务器） -- **global**
  + 会话变量（作用域：一个会话）        -- **session**

  > 使用方法
  >
  > ```mysql
  > -- 查看所有的系统变量
  > show global|session variables; -- global和session指定查看哪种变量，不写就是会话
  > 
  > -- 查看满足条件的部分系统变量
  > show global|session variables like "%char%"; -- 
  > 
  > -- 查看指定的某个系统变量
  > select @@系统变量名；-- 查看会话变量
  > select @@global.系统变量名； -- 查看系统变量
  > 
  > -- 为某个系统变量赋值
  > set 系统变量名 = 值;
  > set @@global.系统变量名 = 值;
  > set global|session 系统变量名 = 值;
  > ```
  >
  > 注意：
  >
  > +  全局变量跨会话有效，不跨重启
  > + 会话变量仅仅针对当前会话有效
  >
  > 

+ 自定义变量 (由用户定义的，自定义变量的作用域是会话层面的)

  + 用户变量 (在整个会话中有效)
  + 局部变量 （仅在begin end中有效）

  >  用户变量使用步骤：
  >
  > 1. 声明
  >
  >    ```mysql
  >    set @my_var = value;  -- 声明时必须初始化
  >    set @my_var := value;
  >    select @my_var := value
  >    ```
  >
  > 2. 赋值
  >
  >    ```mysql
  >    set @my_var = value;  
  >    set @my_var := value;
  >    select @my_var := value
  >    select 字段 into @my_var from 表;  -- 字段必须是一个
  >    ```
  >
  > 3. 使用
  >
  > 局部变量使用步骤
  >
  > 1. 声明
  >
  >    ```mysql
  >    declare 变量名 类型;
  >    declare 变量名 类型 default 值;
  >    ```
  >
  > 2. 赋值
  >
  >    ```mysql
  >    set my_var = value;  -- 变量前不加@符号
  >    set my_var := value;
  >    select @my_var := value  -- 注意，这里要加上@
  >    select 字段 into my_var from 表;  -- 字段必须是一个
  >    ```



## 存储过程

一组预先编译好的sql语句集合，类似于批处理语句, 一旦创建不可修改

#### 创建语法

```mysql
-- 创建存储过程
create procedure 存储过程名(参数列表)   -- 每个参数包括三个部分 :参数模式 参数名 参数类型
begin                                -- 如： in|out|inout name varchar(20)
	一组sql语句
end
```

> 参数模式：(不写默认为in)
>
> + IN    ： 该参数需要输入，和形参Java形参一样
> + OUT  ：该参数可以作为输出。和Java返回值一样
> + INOUT ：该参数既可以作为输入也可以作为输出
>
> 注意：
>
> 1. 如果存储过程只有一条语句，可以省略 begin 和end
> 2. 存储过程体中的每条sql语句必须用分号结尾
> 3. 需要使用delimiter重新设置结束标记，避免存储过程体中的结束符号（;）和mysql默认结束符号  (;)  冲突： `delimiter 结束标记`

#### 调用语法

```mysql
call 存储过程名(实参列表);
```

#### 删除语法

```mysql
drop procedure 存储过程名称;
```

#### 查看存储过程

```mysql
show create procedure 存储过程名;
```

#### 案例

```mysql
delimiter $ -- 设置结束标记,在navicate中，不需要声明结束符，在mysql原生的控制台才需要
-- 案例1：插入记录-------------------------------------------------------------------
 -- 创建+++++++++++++++++++++++++++
 create procedure myProce()
 begin
	 insert into user(username,gender) values("大番茄","男");
 end $
 -- 调用++++++++++++++++++++++++++
 call myProce() $
 
-- 案例2：创建带in参数的存储过程--------------------------------------------------------
 -- 创建+++++++++++++++++++++++++++
 create procedure myProce(in username varchar(20))
 begin
 	select salary form employee where employee.username = username;
 end$
 -- 调用+++++++++++++++++++++++++++
 myProce("sundehui")$
 
-- 案例3：创建带out参数的存储过程------------------------------------------------------
 -- 创建+++++++++++++++++++++++++++
 create procedure myProce(in username varchar(20), out salary int)
 begin
 	select e.salary into salary from employee e where e.username=username;
 end $
 -- 调用+++++++++++++++++++++++++++
 set @salsry $
 myProce("sundehui", @salary) $
 
-- 案例4：传入两个数字，翻倍返回-------------------------------------------------------
 -- 创建+++++++++++++++++++++++++++
 CREATE PROCEDURE myPro1(INOUT a INT, INOUT b INT)
  BEGIN
	set a = a*2;
	set b = b*2;
  END $
 -- 调用+++++++++++++++++++++++++++
 set @a = 10 $
 set @b = 20 $

 CALL myPro1(@a,@b) $
 SELECT @a, @b $
```

## 函数

存储过程和函数的区别：

+ 存储过程可以有0或多个返回值， 适合做批操作
+ 函数有且仅有一个返回值，适合处理拿到的数据，并将处理的结果返回

#### 创建语法

```mysql
create function 函数名(参数列表) returns 返回类型 -- 每个参数包含：参数名 参数类型
begin
	函数体
end
```

> 1. 如果函数只有一条语句，可以省略 begin 和end
> 2. 存储函数中的每条sql语句必须用分号结尾
> 3. 需要使用delimiter重新设置结束标记，避免函数体中的结束符号和mysql默认结束符号冲突： `delimiter 结束标记`

#### 调用语法

```mysql
select 函数名(参数列表);
```

#### 删除语法

```mysql
drop function 函数名;
```



#### 查看函数

```mysql 
show create function 函数名;
```



#### 案例

```mysql
delimiter $
-- 案例1：无参有返回=============================================================
  -- 创建+++++++++++++++++++++++++++++++++++++
  create function myFun() returns int
  begin
    declare res int default 0;
  	select count(*) into res from enployees;
  	return res;
  end$
  -- 调用++++++++++++++++++++++++++++++++++++
  select myFun()$
  
-- 案例2: 有参数有返回
  -- 创建
  create function myFun(username varchar(20)) returns double
  begin 
  	declare res double default 0;
  	select salary into res from employee e where e.username = username;
  	return res;
  end$
  -- 调用
  select myFun("sundehui")$
```



# 配置文件

```ini

[client]

port=3306
# 设置默认字符集为utf8
default-character-set=utf8mb4

[mysql]
no-beep

# 设置默认字符集为utf8
default-character-set=utf8mb4

[mysqld]

port=3306

# 设置默认字符集为utf8
default-character-set=utf8mb4
character-set-server=utf8mb4

# 数据库文件存放地址
datadir=C:/ProgramData/MySQL/MySQL Server 5.7/Data

#默认存储引擎
default-storage-engine=INNODB
#二进制日志，主要用于主从复制
log-bin=C:/ProgramData/MySQL/MySQL Server 5.7/Data/mysqlbin
#错误日志，默认关闭，记录严重警告和错误信息，记录每次启动和关闭的详细信息
log-error=C:/ProgramData/MySQL/MySQL Server 5.7/Data/mysqlerror
#查询日志，用于记录查询的sql语句，开启会降低mysql整体性能
log=C:/ProgramData/MySQL/MySQL Server 5.7/Data/mysqllog
# 和.frm相关的配置
table_definition_cache=1400
```

## 数据文件

+ frm文件

  存储表结构

+ myd文件

  存放表数据

+ myi文件

  存放表索引

# Mysql逻辑架构

![img](https://img2018.cnblogs.com/blog/1485191/201910/1485191-20191008202330054-125227915.png)

#### 连接层

提供客户端连接服务，包含本地socket通信和类似于tcp/ip 的通信。完成一些类似于连接处理、授权认证以及其他安全方案。并且在该层上引入了线程池的概念，为通过验证的客户端连接提供线程。

同样在该层上提供基于ssl的安全连接，服务器也会安全接入的每个客户端验证其所具有的权限。

#### 服务层

核心功能，如sql接口，sql的解析，sql的优化，以及部分内置函数的执行，所有跨存储引擎的功能也在这一层实现，如存储过程、函数等。

在该层，服务器解析查询并创建相应的内部解析树，并对其完成相应的优化，如确定查询表的数据、是否利用索引等，最后执行相应的操作。如果是select语句，服务器还会查询内部缓存，如果缓存足够大，在大量的读操作的环境中性能会很高。

#### 引擎层

mysql的存储引擎真正负责了数据的存取，服务器通过API与存储引擎通信。

#### 存储层

将数据存储到文件系统上，并与存储引擎通信，常用的myisam 和innodb 

![image-20200320220929701](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200320220929701.png)



# Sql性能低下的原因

1. 查询语句写的烂
2. 索引失效
3. 关联查询太多join（设计缺陷或不得已的需求）
4. 服务器调优及各个参数的设置（缓冲区大小，线程数不合适等）



# Sql执行加载顺序

```sql
-- 人的手写顺序
select 
distinct <select_list>
from <left_table>
<join_type> join <right_table>
on <join_condition>
where <where_condition>
group by <group_by_condithion>
having <having_condition>
order by <order_by_condition>
limit <limit_number>

-- sql服务器读取顺序
from <left_table>
on <join_condition>
<join_type> join <right_table>
where <where_condition>
group by <group_by_condithion>
having <having_condition>
select 
distinct <select_list>
order by <order_by_condition>
limit <limit_number>
```

# JOIN的7种情况

1. <img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200320224351745.png" alt="image-20200320224351745" style="zoom:25%;" />  >  

   ```sql
   select <select_list> from tableA A left join tableB B on A.key=B.key;
   ```

   

2. <img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200320224845642.png" alt="image-20200320224845642" style="zoom:25%;" />    

   ```sql
   select <select_list> from tableA A left join tableB B on A.key=B.key where B.key is null;
   ```

   

3. <img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200320224711115.png" alt="image-20200320224711115" style="zoom:25%;" />   

   ```sql
   select <select_list> from tableA A right join tableB B on A.key=B.key;
   ```

   

4. <img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200320225049166.png" alt="image-20200320225049166" style="zoom:25%;" /> 

   ```sql
   select <select_list> from tableA A right join tableB B on A.key=B.key where A.key is null;
   ```

   

5. <img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200320225217502.png" alt="image-20200320225217502" style="zoom:25%;" /> 

   ```sql
   select <select_list> from tableA A inner join tableB B on A.key=B.key;
   ```

6. <img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200320225603410.png" alt="image-20200320225603410" style="zoom:25%;" /> 

   ```sql
   select <select_list> from tableA A 
   full outer join tableB B 
   on A.key=B.key;
   -- 但是mysql不支持全外连接，所以替代方法如下
   select <select_list> from tableA A left join tableB B on A.key=B.key
   union -- 求两个查询的并集，起到去重的作用
   select <select_list> from tableA A right join tableB B on A.key=B.key;
   ```

7. <img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200320225727315.png" alt="image-20200320225727315" style="zoom:25%;" /> 

   ```sql
   select <select_list> from tableA A
   full outer join tableB B
   on A.key=B.key
   where A.key is null or B.key is null;
   -- 但是mysql不支持全外连接，所以替代方法如下
   select <select_list> from tableA A left join tableB B on A.key=B.key
   where B.key is null
   union
   select <select_list> from tableA A right join tableB B on A.key=B.key
   where A.key is null;
   ```



# 索引

除数据外，数据库还维护着一个满足特定查找算法的数据结构，它以某种方式指向数据，这样可以在这些数据结构的基础上实现高级的查找算法，这种数据结构就是索引。

### 什么是索引

索引是帮助数据库快速查找数据的数据结构 >> ==索引是数据结构==

**<font style="color:red">简单理解为排好序的快速查找数据结构</font>**

**索引的目的是提高查找效率，一般来说索引本身也不小，因此往往以文件的形式存储在硬盘中。**

> 平常所说的索引若无特别指明，一般指B树组织的索引，其中聚集索引、次要索引、覆盖索引、复合索引、前缀索引、唯一索引默认都是使用B+树索引。除了B 树索引外还有哈希索引。

### 索引的优劣势

优势：

+ 提高数据检索效率，降低了io成本

+ 通过索引对数据索引，降低数据排序的成本，降低了CPU的消耗

劣势：

+ 降低了表的更新速度，对表更新时不仅要保存数据，还要把索引文件修改

### 索引分类

==一张表的索引做好不要超过5个==

#### 单值索引

一个索引只包含单个列，一个表可以有多个单值索引

#### 唯一索引

索引的值必须唯一，且允许有空值

#### 复合索引

一个索引包含多个列

### 创建的操作

```sql
-- 创建索引
create [unique] index idx_name on table_name(col_name);
alter table_name add [unique] index idx_name on (col_name)
-- 删除索引
drop index [idx_name] on table_name;
-- 查看
show index from table_name;
```

## B+树索引

B+树的结构结构特点为B+树的非叶子节点不存储数据，只有叶子节点才存储数据，非叶子节点存储的是到叶子节点的索引。

### 创建索引的情况

1. 主键自动创建唯一索引
2. 频繁作为查询条件的字段应建立索引
3. 外键建立索引
4. 频繁更新的字段不适合建立索引
5. where条件中用不到的字段不建立索引
6. **在高并发下倾向建立组合索引**
7. 查询排序字段，排序字段如果通过索引访问，将大大提高排序速度、
8. 查询中统计或分组字段
9. 如果某个字段重复较多，不建议键索引



## 覆盖索引

==即索引包含了满足查询结果的数据==

select的数据列只需要从索引中就能获取，而不必读取数据行，MySQL可以利用索引返回select列表中的字段，而不必根据索引再次读取数据文件，换句话说就是查询列要被所建的索引覆盖

但是这并不意味着为所有的字段建立复合索引，那样的话会适得其反

## 性能分析

mysql常见性能瓶颈

+ CPU负担过重
+ IO装入数据远大于内存容量
+ 硬件性能瓶颈



### Explain 

explain关键字可以模拟优化器执行Sql查询语句，从而得知MySQL是如何处理你的sql语句的，来分析你的查询语句或是表结构的性能瓶颈

##### 使用

`explain sql语句`

##### <span>能干嘛，加字段解释</span>

+ 表的读取顺序-------------------------id
+ 数据读取操作的操作类型---------type
+ 哪些索引可以使用-------------------possible_key
+ 哪些索引被实际使用----------------key
+ 表之间的引用--------------------------ref
+ 每张表有多少行被优化器查询-----rows

### Explain结果的分析

![image-20200321005239006](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321005239006.png)

#### 字段解释

##### id

select查询的序列号，包含一组数字，表示查询中执行select子句或操作的顺序

+ id相同，执行顺序由上至下

  ![image-20200321010410156](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321010410156.png)

+ id不同，如果是子查询，id的序号会递增，id越大，优先级越高，越先被执行

  ![image-20200321010435082](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321010435082.png)

+ id相同不同同时存在，id大的先执行，id小的后执行，id相同的从上往下顺序执行

  ![image-20200321010530393](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321010530393.png)

##### select_type

查询的类型，主要用于区别普通查询、联合查询、子查询等复杂查询

+ SIMPLE：简单的select查询，不包含子查询或union
+ PRIMARY：如果sql包含复杂的查询，这是最外层的查询的标记
+ SUBQUERY：子查询
+ DERIVED：衍生表，在from列表中包含的子查询被标记为derived，mysql会递归执行这些子查询，把结果放在**临时表**里
+ UNION：如果第二个select出现在union后则被标记为union，若union包含在from子句的子查询中，外层select被标记为derived
+ UNION RESULT：从union后的表获取结果集的select

##### table

该操作是关于哪张表的

##### type

访问类型，即显示查询使用了何种类型, 类型如下

![image-20200321012129156](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321012129156.png)

<span style="color:red">这些类型从好到差依次是</span>

system > const > eq_ref > ref > range > index>ALL

> + system:表只有一行记录
>
> + const：通过主键或者唯一索引进行一次找到，因为只匹配一行数据，所以可以很快将主键置于where列表中，mysql就能将该查询转换为一个常量
>
> + eq_ref:  唯一索引扫描，对于每个索引键，表中只有一条记录与之匹配 ，也就是索引和记录是一一对应的
>
>   比如一个主键肯定唯一确定一行记录，一个唯一索引肯定唯一确定一行记录；
>
> + ref: 非唯一性索引扫描，对于一个索引键能够找到多条与之匹配的记录
>
> + range: 只检索给定的范围，检索条件必须为索引，且该索引字段值未经过函数处理，且非模糊查询
>
> + index：虽然也是全表扫描，但只遍历索引树，因为索引文件通常较小，因此比ALL快
>
> + all：全表扫描，性能最差
>
> 一般来说，至少得保证查询级别达到range，最好能达到ref



##### possible__keys  与  key

+ possible_keys: 该表可能使用到的索引，有一个或多个，查询涉及到的字段若存在索引则将其列出，但是不一定被查询实际使用
+ key：实际使用到的索引，如果为null，则没有用索引

**也有可能possible_keys 为null， key有值的情况**



##### key_len

其值为索引字段的最大可能长度，并非实际长度，即key_len是根据表的定义得出的，而不是通过检索表得出的

在得到相同的检索结果的前提下，key_len长度越小越好



##### ref

显示索引的哪一列被使用了，如果可能，是一个常数。即哪些列或常量用于查找索引列上的值

![image-20200321130846334](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321130846334.png)

##### rows

根据表统计信息及索引选用情况，大致估算出找到所需记录所需读取的行数 ，在能够达到目标的情况下，rows的值越小越好

![image-20200321131848670](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321131848670.png)

##### extra

包含不适合在其他列中显示但是十分重要的额外信息

###### using filesort

说明MySQL会对数据使用一个外部的索引排序，而不是利用表内的索引顺序进行读取。MySQL中无法利用索引完成的排序操作称为“文件排序”

![image-20200321143003851](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321143003851.png)

**出现这种情况性能不高**

###### using temporary

查询过程中使用了临时表保存结果，常见于排序order by和分组查询group by中

出现这种情况是因为在排序或者分组条件中，没有按创建复合索引的列顺序来使用作为排序条件的索引列，

如下图：

![image-20200321144655827](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321144655827.png)

**出现这种情况会导致性能严重下降**

==以上说明复合索引在使用时必须按照创建索引的索引列顺序使用，即如果索引==

```sql
create index idx_cole_col2 on my_table(col1,col2);
```

==在使用时必须先使用col1，在使用col2，不可以颠倒顺序，或者直接只是用col2==

###### using index

好事，说明查询操作使用到了**覆盖索引**，避免了访问表的数据行，效率不错！！！！

如果同时出现**using where**,说明索引被用来执行索引键值的查找，即用索引键查找索引键值

如果没有同时出现**using where**, 说明索引只被用来作为查询列，而没用于过滤

###### using where

如果出现说明查询中用到了where条件

###### using join buffer

使用了连接缓存

###### impossible where

where的值总是为false，不能用来获取任何元组，即where条件冲突

###### select tables optimized away

![image-20200321152935132](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321152935132.png)

###### distinct

优化distinct操作，在找到第一组匹配的元组后立即停止找同样值的动作



### 性能优化总结

![image-20200321162138612](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321162138612.png)

![image-20200321162245897](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200321162245897.png)



# 锁

## 锁的分类

按数据操作的粒度：

+ 表锁
+ 行锁

按操作类型：

+ 读锁（共享锁）：针对同一份数据可以多个线程同时读取, 拒绝一切更改操作
+ 写锁（排他锁）：针对同一份数据某个时刻只能被一个线程修改，其他线程的读写锁都不能访问

## 表锁

读锁偏向读，偏向MyISAM存储引擎，开销小，加速快，无死锁，锁定粒度大，发生锁冲突的概率大，并发程度最低

### 锁表与解锁表

```mysql
-- 上锁
lock table 表1 read/write, 表2 read/write,...;
-- 解锁
unlock tables;

-- 产看加过锁的表
show open tables;
```

> 当前会话如果对某个表加了读锁，该会话将**只能**对加锁的表进行**读取**，不能对其他未加锁的表进行任何操作（对别的表加锁除外），其他会话读正常，写操作会被阻塞，直到解锁
>
> 如果当前会话对某个表加了写锁，当前会话**只能**对加锁的表进行**读写**，不能对其他未加锁的表进行任何操作（对其他表加锁除外），其他会话读写该表时都会被阻塞，直到解锁

### 分析表锁定

可以通过table_locks_waited和table_locks_immediate状态变量来分析系统上的表锁定

可以通过`show staus like "table_locks%";`来查看这两个系统变量

+ table_locks_waited：出现表级锁定而发生等待的次数，即每发生一次等待就加1。此值高说明存在较为严重的表级锁争用情况
+ table_locks_immediate：产生表级锁定的次数，表示可以立即获取锁的查询次数，每立即获取锁值加1

> **myisam读写锁调度是写优先**，这也是myisam不适合做写为主的表的存储引擎，**它更适合读密集的表**，因为加写锁后其他线程不能做任何操作，大量的更新使查询很难得到锁，从而造成永久阻塞

## 行锁

innoDB与myisam最大的不同一是innodb支持事务，再者就是innodb支持行锁

 开销大，加锁慢；会出现死锁；锁定粒度小，==发生锁冲突的概率低==，并发度高



 ==InnoDB的行锁是实现在索引上的，而不是锁在物理行记录上。潜台词是，如果访问没有命中索引，也无法使用行锁，将要退化为表锁。==



所谓行锁即：

+ 正在修改的哪一行会被上锁，禁止写，但是可以读，当有其他线程来修改时阻塞直至提交

加锁方法

```mysql
-- 假设为id为8 的那一行上锁
begin;
select * from test where id = 8 for update; -- 上锁
.... -- 对id为8的那条记录做出操作
commit; -- 解锁
```



在演示使用行锁时，把自动提交关闭

==当索引失效时，行锁会上升到表锁==



### 分析行锁定

| Innodb_row_lock_current_waits | 1     |         -- 正在等待锁的线程数量
| Innodb_row_lock_time                 | 10114 |    -- 从系统启动到现在锁定的总时间

<span style="color:red">| Innodb_row_lock_time_avg         | 3371   |    -- 从系统启动到现在等待的平均时间</span>

<span style="color:red">| Innodb_row_lock_time_max       | 5837    |    -- 从系统启动到现在等待的最长时间</span>

<span style="color:red">| Innodb_row_lock_waits                | 3          |    -- 系统启动到现在总共等待的次数</span>

以上参数可以通过`show status like "innodb_row_lock_%"`查看

如果等待次数很大，而且每次等待的平均时长也很大时，就需要分析并优化。

**优化：**

+ 尽可能通过索引完成数据检索，避免因无索引导致行锁升级为表锁；
+ 尽量缩小锁定的范围
+ 尽可能少的检索条件，避免间隙锁
+ 合理控制事务大小，减少锁定资源量和资源长度
+ 尽可能降低事务隔离级别

> innodb行锁适用于写密集的表

### 间隙锁的危害

#### 间隙锁

使用范围条件而不是相等条件检索数据，并请求共享或排他锁时，innoDB会给符合条件的已有数据记录的索引项加锁，对于键值在条件范围内但并不存在的记录，叫做间隙，但是innoDB也会对这个间隙加锁，这种锁就是间隙锁；

#### 危害

innoDB会锁定整个范围的索引键值，即使这个键值不存在，因此在锁定期间，插入不存在但是在锁定范围种的记录时会被阻塞直至提交。

 



# 主从复制

> 随着系统应用访问量逐渐增大，单台数据库读写访问压力也随之增大，当读写访问达到一定瓶颈时，将数据库的读写效率骤然下降，甚至不可用。

> 为了解决此类问题，通常会采用MySQL集群，当主库宕机后，集群会自动将一个从库升级为主库，继续对外提供服务；



为了减轻主库的压力，应该在系统应用层面做读写分离，写操作走主库，读操作走从库。分散了数据库的访问压力，提升整个系统的性能和可用性，降低了大访问量引发数据库宕机的故障率。

复制的结果是集群（Cluster）中的所有数据库服务器得到的数据理论上都是一样的，都是同一份数据，只是有多个copy。MySQL默认内建的复制策略是异步的，基于不同的配置，Slave不一定要一直和Master保持连接不断的复制或等待复制，我们可以指定复制所有的数据库，一部分数据库，甚至是某个数据库的某部分的表。

## 同步策略

**同步策略**：Master要等待所有Slave应答之后才会提交（MySql对DB操作的提交通常是先对操作事件进行二进制日志文件写入然后再进行提交）。

**半同步策略**：Master等待至少一个Slave应答就可以提交。

**异步策略**：Master不需要等待Slave应答就可以提交。

**延迟策略**：Slave要至少落后Master指定的时间。



作者：GeekerLou
链接：https://www.jianshu.com/p/c962d9dd9227
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

作者：GeekerLou
链接：https://www.jianshu.com/p/c962d9dd9227
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





先不看