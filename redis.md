# Redis数据库简介

Redis（Remote Dictionary Server )，即远程字典服务，是一个开源的使用ANSI C语言编写、支持网络、**可基于内存亦可持久化**的日志型、Key-Value数据库,它也是一种noSql型的数据库。

## redis的应用

+ 热点信息
+ 任务队列
+ 即时信息查询
+ 时效性信息控制
+ 分布式数据共享
+ 消息队列
+ 分布式锁

## 特点

1. 性能极高 ： 读--11万次/秒， 写 8万次/秒
2. 丰富的数据类型：String, List, Hash, Set, Order set
3. 单线程
4. 原子性：redis的每一个操作都具有原子性，多个操作也支持事务，即将这些操作用`MULTI`和`EXEC`指令包起来
5. 持久化，可以进行数据灾难恢复
6. 丰富的特性：redis还支持publish/subscribe, 通知，key过期等特性
7. 降低数据之间的联系

## 总结

1. redis的单个key存入512mb大小
2. 支持多种数据类型
3. 单线程，原子性
4. 可持久化  因为使用了RDB和AOF机制
5. 支持集群  而且redis支持（0~15）16个库
6. 可以作为消息队列
7. 企业开发中可用作数据库、缓存（热点数据：经常被查，但不经常修改的数据）和消息中间件等功能



# Redis提供的问题解决方案

![image-20200323211318684](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200323211318684.png)



# 安装

安装redis前确保gcc安装:`yum -y install gcc automake autoconf libtool make`

如果运行yum是出现/var.run/yum.pid已被锁定，运行`rm -f  /var/run/yum.pid`



# NOSQL

not-only-sql   --->   非关系型数据库，它是关系型数据库的补充。

它的作用是对海量数据的处理。

特征（相对于关系型数据库）

+ 可伸缩，可扩容
+ 大数据下高性能
+ 灵活的数据类型
+ 高可用



# 电商数据服务解决方案

![image-20200323163315421](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200323163315421.png)

数据库中热点数据的key的命名规则:

​	==表名:主键名:主键值:字段名==

# Redis的基本操作

### 命令帮助查询

如: help get, 会打印如下信息

![image-20200323164729005](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200323164729005.png)

# Redis的数据类型

redis有五种常用数据类型：string, hash, list, set, sorted_set

### string

+ 存储单个数据

+ 存储的内容通常为字符串，如果字符串全部由数字构成，可以作为数字操作使用

+ 操作：

  > 基本操作：
  >
  > + 添加数据
  >
  >   ```shell
  >   # 添加单个数据，已存在该key将其值覆盖
  >   set key value
  >   # 一次添加多个数据
  >   mset key1 val1 key2 val2 ...
  >   ```
  >
  > + 获取数据
  >
  >   ```shell
  >   # 获取单个数据, 不存在返回nil
  >   get key
  >   # 获取多个数据
  >   mget key1 key2 ...
  >   ```
  >
  > + 删除数据
  >
  >   ```shell
  >   del key
  >   ```
  >
  > + 获取字符长度
  >
  >   ```shell
  >   strlen key # 如果过key不存在，返回 0
  >   ```
  >
  > + 追加信息到原始信息的尾部，如果原始信息不存在，就创建
  >
  >   ```shell
  >   append key value
  >   ```
  >
  > 扩展操作：
  >
  > + 设置数值数据增加指定范围的值
  >
  >   ```shell
  >   incr key  # 给key对应的值加1
  >   incrby key increment # 给key对应的值加increment，increment可以为正为负
  >   incrbyfloat key increment # 给key对应的值增加一个小数
  >   ```
  >
  > + 设置数值数据减少指定范围的值
  >
  >   ```shell
  >   decr key  # 给key对应的值减1
  >   decrby key decrement # 给key对应的值减decrement，decrement可以为正为负
  >   ```
  >
  > + 设置数据具有指定的声明周期
  >
  >   ```shell
  >   setex key seconds value
  >   psetex key milliseconds value
  >   ```
  >
  >   

### hash

![image-20200323173140742](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200323173140742.png)

由上可知，hash是一个存储空间保存多个键值对数据

hash存储结构的优化：

+ 如果field数量较少，存储结构优化为类数组结构
+ 如果field数量较多，存储结构优化为HashMap结构

**操作**

> 基本操作
>
> + 添加/修改数据
>
>   ```shell
>   hset key field value
>   hmset key field1 value1 field2 value2 ....
>   ```
>
> + 获取数据
>
>   ```shell
>   hget key field
>   hmget key field1 field2 ...
>   hgetall key
>   ```
>
> + 删除数据
>
>   ```shell
>   hdel key field1, field2 ...
>   ```
>
> + 获取hash表中字段的数量
>
>   ```shell
>   hlen key
>   ```
>
> + hash 表中是否存在指定字段
>
>   ```shell
>   hexists key field
>   ```
>
> 扩展操作：
>
> + 获取hash表中所有字段名或字段值
>
>   ```shell
>   hkeys key
>   hvals key
>   ```
>
> + 设置hash表的指定字段的数值数据增加指定范围的值
>
>   ```shell
>   hincrby key field increment
>   hincrbyfloar key field increment
>   ```
>
> + 如果field有值，就不做操作，如果不存在field就添加
>
>   ```shell
>   hsetnx key field value
>   ```
>
>   



注意事项：

+ hash的value只能存储字符串，不允许再存其他类型的数据
+ 每个hash可以存储2<sup>32</sup>-1个键值对
+ 尽量不要用hash存储对象
+ hgetall可以获取全部属性，如果field过多，遍历整体数据的效率就会很低，有可能称为数据访问的瓶颈

### list

特点：

+  存储多个数据，并对存入数据的顺序进行区分
+ 底层使用双向链表



操作：

> 基本操作：
>
> + 添加/修改数据
>
>   ```shell
>   lpush key value1 value2 ... # 从左边添加数据
>   rpush key value1 value2 ... # 从右边添加数据
>   ```
>
> + 获取数据
>
>   ```shell
>   lrange key start stop # 从左侧开始查询从start到stop的数据，start从0开始,-1代表最后一个
>   lindex key index # 查询索引为index的值
>   llen key # 查询列表的长度
>   ```
>
> + 获取并移除数据
>
>   ```shell
>   lpop key
>   rpop key
>   ```
>
> 扩展操作
>
> + 规定时间内获取并移除数据
>
>   ```shell
>   blpop key1 key2 timeout
>   brpop key2 key2 timeout
>   ```
>
> + 移除指定列表的指定个数据的某个数据
>
>   ```shell
>   lrem key count value
>   ```



注意：

+ list中的数据都是string的，list最大存储个数为2<sup>32</sup>-1个
+ list具有索引的概念，但是其最主要的是队列或者栈的形式
+ list可以使用分页，通常第一页数据来自于list，第二页甚至更多的数据通过数据库加载

应用场景

+ 依赖list的顺序特征来管理信息
+ 使用队列解决多路信息汇总合并的问题
+ 使用栈模型解决最新消息问题



### set

满足的业务场景：存储大量数据，再查询时具有更高的效率

set是基于hash的，与hash的存储结构完全相同，不同点是set仅存储域，不存储值

![image-20200323193839159](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200323193839159.png)

操作：

> 基本操作:
>
> + 添加数据
>
>   ```shell
>   sadd key memeber1 memeber2 ...
>   ```
>
> + 获取全部数据
>
>   ```shell
>   smemebers key
>   ```
>
> + 删除数据
>
>   ```shell
>   srem key memeber1, memeber2, ...
>   ```
>
> + 获取集合长度
>
>   ```shell
>   scard key
>   ```
>
> + 判断集合是否包含某个成员
>
>   ```shell
>   sismemeber key memeber
>   ```
>
> 扩展操作
>
> + 随机获取集合中指定数量的数据
>
>   ```shell
>   srandmemeber key [count]
>   ```
>
> + 随机回去集合中指定数量数据，并将其移除
>
>   ```shell
>   spop key [count]
>   ```
>
> + 求两个集合的交、并、差集
>
>   ```shell
>   sinter key [key ...]
>   sunion key [key ...]
>   sdiff key [key ...]
>   ```
>
> + 求两个集合的交、并、差集，并将结果存储到指定集合中
>
>   ```shell
>   sinterstore destination key [key ...]
>   sunionstore destination key [key ...]
>   sdiffstore destination key [key ...]
>   ```
>
> + 将指定数据从原始数据集中迁移到目标数据集
>
>   ```shell
>   smove source destination memeber
>   ```



注意：

+ set中不允许重复，添加重复数据返回0（false）
+ set虽然与hash的存储结构一样，但是不能启用hash中存储值的空间

### sorted_set

sorted_set 不仅满足存储大量数据，再查询时具有更高的效率的需求，而且其中的数据是有序的

![image-20200323201747823](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200323201747823.png)

==sorted_set提供了score字段，这个字段是专门用来排序用的，切记不要把score当作数据，在往集合中放数据时要提供score==

操作：

> 基本操作：
>
> + 添加数据
>
>   ```shell
>   zadd key score  member [score memeber ...]
>   ```
>
> + 获取全部数据
>
>   ```shell
>   zrange key start stop [withscores]
>   zrevrange key start stop [withscores]
>   ```
>
> + 删除数据
>
>   ```shell
>   zrem key memeber [member ...]
>   ```
>
> + 按条件获取数据
>
>   ```shell
>   zrangebyscore key min max [withscores] [limit]
>   zrevrangebyscore key min max [withscores] [limit]
>   ```
>
> + 按条件删除数据
>
>   ```shell
>   zremrangebyrank key start stop # 按照索引删除
>   zremrangebyscore key min max # 按照score删除
>   ```
>
> + 获取集合长度
>
>   ```shell
>   zcard key
>   zcard key min max
>   ```
>
> + 集合交并
>
>   ```shell
>   zinterstore destination numkeys key [key ...]
>   zunionstore destination numkeys key [key ...]
>   ```
>
> 扩展操作
>
> + 获取数据对应的索引（排名）
>
>   ```shell
>   zrank key memeber
>   zrevrank key memeber
>   ```
>
> + score值的获取与修改
>
>   ```shell
>   zscore key member
>   zincrby key increment member
>   ```
>
>   



注意：

+ score的数据存储空间是64位
+ score的小数是一个双精度double，注意精度丢失，导致排序不准
+ sorted_set底层存储是依赖set的，所以数据不可重复，如果添加相同数据，会覆盖score，只保留最后修改的结果

使用场景：

+ 存储带有权重的任务或者消息，优先处理权重高的任务，用score存储权重



# Redis的通用命令

### 对key的基本操作

+ 删除指定key `del key [key ...]`
+ key是否存在 `exists key`
+ key 的数据类型 `type key`

### 对key的扩展操作

##### 为key设置有效期

```shell
expire key secondes
pexpire key milliseconds
expireat key timestamp
pexpireat key  milliseconds-timestamp
```

##### 获取key的剩余有效时间

```shell
ttl key  # 如果key是永久性的，返回值为-1，如果key不存在，返回值为-2
pttl key
```

##### 切换key从时效性变为永久性

```shell
persist key
```

##### 按照模式查询key

```shell
keys pattern # pattern 是一个模式，例如 keys * 就是查询所有key
```

![image-20200323212708319](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200323212708319.png)

##### 为key改名

```shell
rename key newkey # 可以覆盖已存在的key
renamenx key new key # 不能覆盖已存在的key
```

##### 对所有key排序

```shell
sort key # 不改变原数据
```

##### 其他key通用操作

```shell
help @generic
```



### 数据库通用操作

随着数据量的增加，redis中的数据不分类别混在一起，极易出现冲突，因此redis数据库为每个服务提供了 16个数据库，编号为0~15，每个数据库之间相互独立

#### 数据库的基本操作

##### 切换数据库

```shell
select index # 切换到编号为index的数据库，默认在0区
```

##### 数据移动操作

```shell
move key db_index # 将当前数据库的key移动到db_index数据库中, 当前数据库不存在key
                  # 如果db_index 库中有key，则移动失败，两个库中的数据不做改变
```

##### 数据清除

```shell
dbsize # 查看当前库中有多少个key
flushdb # 清除当前库中的所有数据
flushall #清除所有库的所有数据
```

##### 其他操作

```shell
quit # 退出
ping # 测试redis服务是否联通
echo # 在控制台输出消息
```

# Jedis

Jedis是java语言用来连接redis服务的，处理使用jedis，Java还可以使用 

SpringData Redis、Lettuce

==jedis提供的方法名和redis的指令是一样的==

```java
        // 1.连接redis
        Jedis jedis = new Jedis("192.168.88.130", 6379);
        // 2.操作redis
        String myname = jedis.get("myname");
        System.out.println(myname);
        // 3.关闭连接
        jedis.close();
```





# Redis的配置文件

``` shell
bind 127.0.0.1  # 绑定的主机ip
port 6379       # 服务绑定的端口号
daemonize no    # 是否启动守护线程，即后台运行redis服务
logfile ""      # 日志文件的名称
dir ./          # 当前服务的文件的保存位置

#=============================RDB的配置============================================
dbfilename dump.rdb  # 为备份文件指定名字，通常为dump-端口号.rdb
rdbcompression yes   # 是否压缩持久化数据，使用LZF压缩，默认开启，如果为no可以节省cpu资源，但是会					   导致持久化文件较大
rdbchecksum yes      # 是否进行rdb文件格式校验，该校验过程在读写过程均进行，默认开启，如果关闭可以                        节约读写过程约10%的时间消耗，但是有数据损坏的风险
stop-writes-on-bgsave-error yes  # 后台存储过程中如果出现错误，是否停止操作，默认为yes
save secondes changes  # 在seconds时间内，key的变化数量达到changes的数量就进行持久化

#============================AOF的配置==============================================
appendonly yes        # 开启aof持久化
appendfsync everysec  # 指定aof写策略
appendfilename appendonly-6379.aof  # 指定apf文件名
#                  ==========AOF重写===========
auto-aof-rewrite-min-size    size  # 设置重写缓冲区临界值
auto-aof-rewrite-percentage    percentage  # 设置重写缓冲区百分比
```

# Redis的启动

## 指定参数启动

在启动redis时可以指定端口

`redis-server --port 端口号`

对应的，连接客户端也要指定端口

`redis-cli -p 端口号` 

补充：

+ 为客户的指定主机ip `redis-cli -h 主机ip`
+ 为客户端通过是指定主机ip和端口号 `redis-cli -h 主机ip -p 端口号`

## 指定配置文件启动

redis-server redis-6379.conf  



# Redis的持久化

### 持久化保存什么？

+ 为当前数据打一个快照，以快照的形式保存当前数据的状态，存储的是数据结果，存储格式简单，关注点在数据（RDB）
+ 将对当前数据的操作过程保存到日志中，以日志的形式保存数据，存储的是操作过程，存储格式复杂，关注点在数据的操作过程（AOF）

### RDB持久化

**命令的执行考虑的三点：谁、什么时间、干什么事**

+ 谁：redis操作者（用户）

+ 什么时间：即时（随时进行）

+ 干什么事：保存数据

  

**持久化执行的命令**

`save`, 手动执行一次保存数据的快照信息



#### 配置文件

见Redis的配置文件

#### RDB使用save持久化的缺点

==在进行rdb的save持久化时可能会消耗大量时间和cpu性能下降，进而造成阻塞，因此不要在线上使用==

#### bgsave

bgsave解决了save的缺点

**命令的执行考虑的三点：谁、什么时间、干什么事**

+ 谁：redis操作者（用户）；redis服务器实际控制指令执行

+ 什么时间：即时（发起）；合理时间执行

+ 干什么事：保存数据

![image-20200324002132857](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324002132857.png)

==gbsave是针对dave阻塞问题做出的优化，redis内部所有涉及到rdb的操作都采用了bgsave的方式，save可以放弃使用==



#### 自动执行持久化

无论是save还是bgsave都手动持久化，如果忘了怎么办？不知道数据产生了多少变化，怎么判断是否保存？

**命令的执行考虑的三点：谁、什么时间、干什么事**

+ 谁：redis服务器发起指令（基于条件）

+ 什么时间：满足条件

+ 干什么事：保存数据

在配置文件中添加`save seconds changes`

该配置意思是：在seconds时间内，key的变化数量达到changes的数量就进行持久化

+ seconds：监控时间范围
+ changes：监控key的变化量

![image-20200324003507713](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324003507713.png)

#### RDB三种持久化对比

![image-20200324003645034](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324003645034.png)

注：save配置使用到了bgsave



#### RDB的特殊持久化方式

+ 全量复制：见主从复制
+ 服务器运行过程中重启:  `debug reload`
+ 关闭服务器时指定保存数据:  `shutdown save`, 这个命令是在 redis-cli 中执行

#### RDB的优缺点

**优点**

+ 存储效率高，因为它的文件是一个紧凑的二进制文件
+ 其内部存储的是在某个时间点的数据快照，非常适合数据备份，全量复制等场景
+ RDB恢复数据比AOF快很多
+ 应用：服务器中每x小时执行bgsave，并将备份文件拷贝到远程及其，用于灾难恢复

**缺点**

+ 无论是使用指令还是配置都无法做到实时持久化，具有较大可能性丢失数据
+ 数据量大，导致io性能低
+ 宕机带来数据丢失
+ bgsave指令每次执行都要fork子进程，消耗一部分性能
+ redis的各个版本中rdb的格式尚未统一，可能出现格式不兼容问题

### AOF持久化

由于rdb的缺点，AOF有如下改进

+ 不写全部数据，只写部分数据
+ 从记录数据改为记录过程
+ 对所有操作均进行记录，排除丢失数据风险

#### AOF简介

append only file，以独立日志的方式记录每次写命令，重启时再次执行AOF文件中的命令，恢复数据

==AOF主要作用是解决了数据持久化的实时性，已成为主流的持久化方案==

#### AOF写数据的过程

![image-20200324143038003](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324143038003.png)

#### AOF写数据的策略

+ always（每次）-- 不建议使用

  每次写入缓冲区操作均同步到aof文件，数据零误差，但是性能低

+ everysec（每秒） -- 默认，也是建议使用

  每秒将写入缓冲区的数据同步到aof文件，准确率较高，性能较高，宕机仅丢失一秒数据

+ no（系统控制）

  由操作系统控制每次同步到aof文件的周期，过程不可控

#### AOF功能开启

1. 配置1

   `appendonly yes|no`

   是否开启aof持久化功能，默认不开启

2. 配置2

   `appendfsync always|everysec|no`

   指定aof写数据的策略

#### AOF重写

随着命令的不断写入，aof文件会越来越大，为了解决这个问题，Redis引入重写机制压缩文件体积。

AOF文件重写是将Redis进程内的数据转化为写命令同步到新AOF文件的过程。简单而言，将对同一个数据的若干条命令执行结果转化为最终结果数据对应的指令，并将该指令记录到AOF文件中。

**作用**

+ 降低磁盘占用
+ 提高持久化效率，降低持久化写时间，提高io效率
+ 降低数据恢复用时，提高数据恢复效率

#### AOF重写规则

+ 进程内已经超时数据不再写入文件

+ 忽略无效指令，重写时使用进程内数据直接生成，这样新的aof文件只保留数据的最终写入命令

+ 对同一数据的多条写命令合并为一条命令

  如：rpush myList a,rpush myList b,rpush myList b 合并为  rpush myList a, b, c

  为防止数据量过大造成客户端缓冲区溢出，对 list ，hash，set，sorted_set类型规定，每条指令最多写入64个元素

#### AOF重写方式

**方式1：手动重写**

```shell
bgrewriteaof
```

![image-20200324150532458](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324150532458.png)

**方式2：自动重写**

```shell
# aof自动重写触发条件的两种设置
#    当缓冲区的内容大于size时，触发重写，默认size较大，一般为32mb或64mb
auto-aof-rewrite-min-size    size
#    当缓冲区的内容达到百分比的时候，触发重写
auto-aof-rewrite-percentage    percentage
```

那么触发条件怎么用？自动触发对比参数如下

```shell
# 开启aof持久化，在redis-cli中执行info persistence，可以看到如下两个自动触发对比参数
aof_current_size
aof_base_size
```

1. 当`aof-current-size > auto-aof-rewrite-min-size`时，自动触发重写$ \Gamma(z) = \int_0^\infty t^{z-1}e^{-t}dt\,. $

2. 当$\displaystyle\left(\frac{aof-current-size \ - \ aof-base-size}{aof-base-size}\right)>=auto-aof-rewrite-percentage$  时触发重写

#### AOF持久化和重写流程

![image-20200324154218917](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324154218917.png)

==由上可见，always重写规则是没有aof缓冲区的==

![image-20200324154452406](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324154452406.png)

当执行重写时，主进程fork一个子进程，子进程从aof重写缓冲区中取数据，将其写入重写后的aof文件，写入完成后，将该文件替换原来的aof文件，最终能看到的就是重写后的新文件

### RDB和AOF对比

![image-20200324154942628](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324154942628.png)

![image-20200324155142825](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324155142825.png)

### 持久化的应用场景

如果A数据需要持久化，B数据不需要持久化，那么应该把A和B放在两个redis服务器中，注意，不是同一个服务器的两个数据库



# Redis事务

### 事务的基本操作

1. **开启事务**

   ```shell
   multi
   ```

   作用：设置事务的开启位置，该指令后的所有操作均加入到该事务队列中

2. **执行事务**

   ```shell
   exec
   ```

   作用：设置事务的结束位置，同时执行事务队列的指令。与multi成对出现，成对使用

3. **取消事务**

   ```shell
   discard
   ```

   作用：销毁事务队列，执行该指令后，exec指令无效

### 事务的工作流程

<img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324161803140.png" alt="image-20200324161803140" style="zoom: 50%;" />   <img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200324161511729.png" alt="image-20200324161511729" style="zoom: 33%;" />

服务器接受指令，判断当前是否是事务状态，是则识别命令，如果命令是普通指令，则加入右侧的队列中，返回”QUEUED“, 如果不是普通指令，如EXEC指令，则执行队列中的指令，并返回执行结果；如果是DISCARD，则销毁队列，返回“OK”

### 注意事项

1. 定义事务过程中，命令格式输入错误，则立即终止事务，同时销毁事务队列，也就是正确的指令也会被销毁
2. 定义事务过程中，命令执行出错，则不会影响正确的指令执行，但是错误的指令执行时报错

==这也说明了，命令在进事务队列时并不进行验证，只是单纯的把命令塞进队列中，在执行时才能发现错误；再者，事务在执行过程中出错不会自动回滚，需要手动回滚;==

### 基于特定条件的事务执行--锁

#### 监视锁

1. 对key添加监视锁，在执行exec前如果key发生变化，终止事务执行

   ```shell
   watch key [key ...]
   ```

2. 取消对所有key的监视

   ```shell
   unwatch
   ```

==redis可以用于基于状态控制的批量任务执行==

**watch不能在事务中执行**

#### 分布式锁

1. 使用setnx设置一个公共锁

   ```shell
   setnx lock-key value # 如果key已经存在，则不修改其值，否则创建
   expire lock-key seconds # 为锁设置一个超时时间，超时不释放锁则销毁锁
   pexpire lock-key milliseconds
   ```

   setnx返回值是布尔值，如果key存在，返回0，若不存在返回1

   对返回1的，拥有控制权，进行下一步业务操作

   对返回0的，不具有控制权，排队等侯

   操作完毕直接用del删除这个锁（lock-key）