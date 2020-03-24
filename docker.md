# Docker简介

docker是一种容器技术，解决软件跨环境迁移问题。

# 安装Docker

centos7为例

```shell
# 1. 更新yum包
yum update
# 2. 安装需要的软件包,yum-utils提供yum-config-manager功能，另外两个是device-mapper驱动依赖的
yum install -y yum-utils device-mapper-persistent-data lvm2
# 3. 设置yum源
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# 4. 安装docker，-y参数表示一路yes
yum install -y docker-ce
# 5. 查看docker版本
docker -v
```



# Docker架构

![image-20200322140138220](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200322140138220.png)

+ 镜像(image)：相当于一个完整的root文件系统，类似于操作系统镜像。
+ 容器(container)：容器是使用镜像创建的动态实体。它可以被创建、启动、停止、删除、暂停等。
+ 仓库(repository)：用来保存镜像的地方

# 配置Docker镜像加速器

默认情况情况下，镜像都从[docker hub](https://hub.docker.com)上下载，但是其服务器在国外，国内下载慢，一般都配置国内镜像。

以阿里云镜像为例：

登录阿里云-> 控制台->左上角菜单->产品与服务->搜索"镜像"->容器镜像服务->镜像加速器->复制连接 

https://zdgmgmg0.mirror.aliyuncs.com

# Docker命令

### Docker服务相关命令

这一部分命令和deamon相关

1. 启动docker服务

   ```shell
   # 启动
   systemctl start docker
   # 查看状态
   systemctl status docker
   ```

2. 停止docker服务

   ```shell
   # 停止
   systemctl stop docker
   # 查看状态
   systemctl status docker
   ```

3. 重启docker服务

   ```shell
   # 重启
   systemctl restart docker
   ```

4. 查看docker服务状态

   ```shell
   systemctl status docker
   ```

5. 开机启动docker服务

   ```shell
   systemctl enable docker
   ```

   

# Docker镜像相关命令

1. 查看镜像

   + 查看本地镜像

     ```shell
     docker images    # 查看本机镜像的详细信息
     docker images -q # 查看所有镜像的image id
     ```

2. 搜索镜像

   ```shell
   docker search redis
   ```

3. 拉取镜像

   ```shell
   # 如果不加版本号默认是最新版
   docker pull redis:5.0
   ```

   

4. 删除镜像

   ```shell
   docker rmi  imge的id
   docker rmi `docker images -1` # 删除本地全部镜像
   ```

   

### 查看docker支持的版本号信息

访问[hub.docker.com](hub.docker.com)网站，搜索redis，可以看到docker支持的redis版本号tag，在拉取镜像时就可以添加这些版本号tag，但是同一个版本的镜像可以有不同的版本号tag

# Docker容器相关命令

1. 创建容器

   ```shell
   # -i:表示容器一直运行
   # -t:给容器分配一个伪终端：创建的是一个交互式容器
   # -d:创建完容器不立即进入容器，需要时进入，用eixt退出容器后，容器依旧可以后台运行：创建的是一个守护式容器
   # --name:容器名
   # /bin/bash:进入容器的初始化指令
   docker run -i -t --name=容器名  镜像名:版本号(如: cetos:7) /bin/bash
   docker run -i -d --name=容器名  镜像名:版本号(如: cetos:7) /bin/bash
   ```

2. 进入容器

   ```shell
   docker exec -i -t 容器名 /bin/bash # 进入容器再exit退出后容器不会关闭
   ```

3. 关闭容器

   ```shell
   docker stop 容器名
   ```

4. 开启容器

   ```shell
   docker start 容器名
   ```

5. 退出容器

   ```shell
   exit # 通过-i参数创建的容器用这种方法推出后，容器会关闭
   ```

6. 查看容器

   ```shell
   # 查看正在运行的容器
   docker ps
   # 查看全部容器
   docker ps -a
   # 查看所有容器的id
   docker ps -aq
   # 查看容器的全部信息
   docker inspect 容器名
   ```

7. 删除容器（正在运行的容器是不能删除的）

   ```shell
   docker rm 容器id/容器名
   # 删除所有容器
   docker rm `docker ps -aq`
   ```

   

# Docker容器数据卷

## 数据卷

+ 数据卷是宿主机中的一个目录或文件
+ 当容器目录数据卷绑定后，对方的修改会立即同步
+ 一个数据卷可以被多个容器挂载
+ 一个容器可以被挂在对各数据卷

### 数据卷的作用

+ 容器数据持久化
+ 外部机器和容器间的通信
+ 容器之间的数据交互

## 配置数据卷

在创建容器时，使用-v参数设置数据卷

```shell
docker run ... -v 宿主机目录(文件):容器内目录(文件) ...
```

### 注意事项

+ 目录或文件必须时绝对路径
+ 如果目录不存在会自动创建
+ 可以挂在多个数据卷



## 配置数据卷容器

数据卷容器就是用来统一保存数据卷的容器，和其他容器没有本质的区别

### 创建数据卷容器

![image-20200322174419260](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200322174419260.png)

==创建c3容器后，docker已经在宿主机中创建了一个数据卷目录，可以通过`docker inspect c3`查看mounts下的source字段，查看宿主机中的数据卷目录的位置，c1,c2也挂载到了这个数据卷。即便c3，c2，c1，中有一个挂了，也不影响另外两个通信==

## 数据卷小结

1. 概念
   + 宿主机的一个文件或目录
2. 数据卷作用
   + 容器数据持久化, 即便删除容器，数据卷中的数据也不会丢失
   + 宿主机与容器进行数据交换
   + 容器间进行数据交换
3. 数据卷容器
   + 创建一个容器挂载一个目录，让其他容器的数据卷继承自该容器`--volume-from `



# Docker应用部署

## Mysql部署

步骤：

1. 搜索mysql镜像

   ```shell
   docker search mysql
   ```

2. 拉取mysql镜像

   ```shell
   docker pull mysql:5.7
   ```

3. 创建mysql容器: 设置端口映射和目录映射

   ```shell
   # 在/root目录下创建mysql目录，用来存放mysql的数据信息
   mkdir /root/mysql
   cd /root/mysql
   
   docker run -i -d   \
   -p 3306:3306   \   # 把容器的3306端口映射到宿主机的3306端口上
   --name=容器名
   -v $PWD/cnf:/etc/mysql/conf.d   \
   -v $PWD/logs:/logs   \
   -v $PWD/data:/var/lib/mysql   \
   -e MYSQL_ROOT_PASSWORD=sundehui   \ # 初始化root用户
   mysql:5.7
   ```

4. 操作容器中的mysql

注意：通过以上命令创建mysql容器后，mysql已经启动

注意：

1. 容器内的网络服务是无法和外部机器直接通信的
2. 外部机器和宿主机可以直接通信
3. 宿主机和容器可以直接通信
4. 当容器中的服务要被外部机器访问是，可以把容器提供该服务的端口映射到宿主机上的某个端口，外部机器访问宿主机的这个端口，从而访问容器的服务，这称为**端口映射**

<img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200322181450621.png" alt="image-20200322181450621" style="zoom: 50%;" />

## Tomcat部署

步骤：

1. 搜索tomcat镜像

   ```shell
   docker search tomcat
   ```

2. 拉取tomcat镜像

   ```shell
   docker pull tomcat
   ```

3. 创建tomcat容器，设置端口映射和目录映射

   ```shell
   # 在/root目录下创建tomcat目录，用来存放tomcat的数据信息
   mkdir /root/tomcat
   cd /root/tomcat
   
   docker run -id \
   --name=tomcat9 \
   -p 8080:8080 \  # 映射端口
   -v $PWD:/usr/local/tomcat/webapps \  # 把webapps目录挂载到宿主机的当前目录
   tomcat
   ```

注意：

+ 通过以上命令创建tomcat容器后，tomcat已经启动

## Nginx部署

步骤：

1. 搜索

   ```shell
   docker search nginx
   ```

2. 拉取

   ```shell
   docker pull nginx:1.17
   ```

3. 创建nginx容器: 设置端口映射和目录映射

   ```shell
   # 在/root目录下创建nginx目录，用来存放nginx的数据信息
   mkdir /root/nginx
   cd /root/nginx
   mkdir conf
   cd conf
   touch nginx.conf
   
   # 创建nginx容器
   docker run -id --name=c_nginx \
   -p 80:80 \
   -v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf \ 
   -v $PWD/logs:/var/log/nginx \
   -v $PWD/html:/usr/share/nginx/html \
   nginx:1.17
   ```

   

## Redis部署

步骤：

1. 搜索

   ```shell
   docker search redis
   ```

2. 拉取

   ```shell
   docker pull redis:5.0
   ```

3. 创建redis容器: 设置端口映射

   ```shell
   docker run -id --name=c_redis -p 6379:6379 redis:5.0
   ```

4. 使用外部机器连接redis服务

   ```shell
   ./redis-cli.exe -h 宿主机ip -p 6379
   ```



# Dockerfile

## docker镜像原理

+ docker镜像是由特殊的文件系统叠加而成

+ 最底层是bootfs，并且使用的是宿主机的bootfs--意味着镜像和宿主机使用了相同的启动引导系统和内核

+ 第二层是rootfs，称为base image

+ 在往上可以叠加其他镜像 

+ 这种叠加称为统一文件系统，该技术将不同层整合成一个文件系统，这些层提供了一个统一视角，进而隐藏了多层的存在，因此在用户看来只存在一个文件系统

+ 一个镜像可以放在另一个镜像上面，下面的镜像称为父镜像，最底层的称为基础镜像

  <img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200322210730326.png" alt="image-20200322210730326" style="zoom:50%;" />



==分层的目的是复用，只读：这些镜像不能改==

如果执意要改

+ 当从一个镜像启动容器时，Docker会在最顶层加载一个读写文件系统作为容器

<img src="C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200322211747797.png" alt="image-20200322211747797" style="zoom:50%;" />



小结：

+ docker本质是一个分层的文件系统

+ 之所以一个centos镜像只有200mb左右，是因为它复用了宿主机的bootfs，因此该镜像只有rootfs和其他镜像层

+ 为什么tomcat镜像有600mb？

  由于docker镜像是分层的，虽然一个tomcat软件只有70mb，但是它需要依赖父镜像和基础镜像，所以整个镜像有600多mb



## 制作镜像

### 方法一：从容器转为镜像

```shell
docker commit 容器id 输出的镜像名:版本号
```

但是镜像无法传输，所以可以将其转换为压缩文件

```shell
docker  save -o 压缩文件名 镜像名:版本号
```

获得该压缩文件后，解压

```shell
docker load 压缩文件名
```

==如果原容器有挂载数据卷，那么重新生成的容器将不保留这些数据==

### 方法二：dockerfile

dockerfile是用来制作docker镜像的文件，

+ 包含了一条条指令
+ 每一条指令构建一层，基于基础镜像，最终构建出一个新的镜像
+ 对于开发人员，它可以为开发团队提供一个完全一致的开发环境
+ 对于测试人员，可以直接拿开发时所构建的镜像或者dockerfile构建一个新的镜像开始工作
+ 对于运维人员，在部署时可以实现应用的无缝移植

#### 关键字：

| 关键字       | 作用                     | 说明                                                         |
| ------------ | ------------------------ | ------------------------------------------------------------ |
| FROM         | 指定父镜像               | 指定dockerfile基于那个镜像构建                               |
| MAINTAINER   | 作者信息                 | 标明dockerfile是谁写的                                       |
| LABEL        | 标签                     | 用来标明dockerfile的标签，可以使用LABEL代替MAINTAINER，最终都可以在docker image基本信息中查看 |
| RUN          | 执行命令                 | 容器构建时执行一段命令，默认是/bin/sh格式 ，RUN command或者RUN["command","param1","param2"] |
| CMD          | 容器启动命令             | 提供容器启动时的默认命令，和ENTRYPOINT配合使用，CMD command param1 param2 或者 CMD ["command","param1","param2"] |
| ENTRYPOINT   | 入口                     | 一般在制作一些执行就关闭的容器中使用                         |
| COPY         | 复制文件                 | build时将文件复制到image中                                   |
| ADD          | 添加文件                 | build时将文件添加到image中，不仅仅局限于当前build上下文，也可以来源于远程服务 |
| ENV          | 环境变量                 | 指定build时的环境变量，可以在启动容器时通过参数 -e 覆盖，格式ENV name=value |
| ARG          | 构建参数                 | 只在构建时使用到，如果有ENV，相同的名字会被ENV的值覆盖       |
| VOLUME       | 定义外部可以挂载的数据卷 | 指定在duild时，image的哪些目录可以挂载到文件系统中，启动容器时使用 -v 绑定，格式 VOLUME ["目录"] |
| EXPOSE       | 暴露端口                 | 定义容器运行时监听的端口，构建容器时使用 -p 将暴露的端口和宿主机端口绑定，格式  EXPOSE  8080 或者 EXPOSE  8080/udp |
| WOEKDIR      | 工作目录                 | 指定容器内部的工作目录，即一进到容器时的目录，如果没有创建则自动创建 |
| USER         | 指定执行用户             | 指定build或启动时                                            |
| HEALTHYCHECK | 健康检查                 |                                                              |
| ONBUILD      | 触发器                   |                                                              |
| STOPSIGNAL   | 发送信号量到宿主机       |                                                              |
| SHELL        | 指定执行脚本的shell      |                                                              |

#### 案例

**案例一：**自定义centOS7镜像，要求：

1. 默认登录路径为 /usr

2. 可以使用vim

实现步骤:

1. 定义父镜像: FROM centos:7
2. 定义作者信息: MAINTAINER davidswin@qq.com
3. 执行安装vim的命令: RUN yum install -y vim
4. 设置登录路径：WORKDIR /usr
5. 定义容器启动执行的命令: CMD /bin/bash

构建：

```shell
# -f 指定dockerfile文件的路径
# -t 指定镜像名和版本号
docker build -f ./my_dockerfile  -t 镜像名:版本号 .
```

**案例二：**定义dockfile，发布springboot项目

实现步骤：

首先把springboot项目打成jar包，将jar包上传到服务器中，与要编写的dockerfile处在同一目录下

1. 定义父镜像  FROM java:8
2. 定义作者信息  MAINTAINER davidswin@qq.com
3. 将jar包添加到容器  ADD my_spring-boot.jar app.jar                   -- app.jar是为my_spring-boot.jar取的别名
4. 定义容器启动执行的命令:CMD java -jar app.jar 

构建：

```shell
docker build -f ./my_dockerfile -t 镜像名:版本号 . # 不要忽略  .  
```





# Docker服务编排

微服务架构的应用系统中一般包含若干个微服务，每个微服务一般都会部署多个实例，如果每个微服务都需要手动启动停止，工作量会很大。

所谓服务编排就是按照一定的业务规则批量管理容器

### DockerCompose

Docker Compose是一个编排多容器分布式部署的工具，提供命令集管理容器化应用的完整开发周期，包括服务构建，启动，停止。使用步骤：

1. 利用dockerfile定义运行环境镜像
2. 使用docker-compose.yml定义组成应用的各服务
3. 运行docker-compose up 启动应用

### 安装Docker Compose

```shell
# 按装前要先安装docker
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
# 设置文件的执行权限
chmod +x /usr/local/bin/docker-compose
# 查看版本信息
docker-compose -version
```

### 卸载Docker Compose

```shell
# 由于是二进制包安装的，删除二进制文件即可
rm /usr/local/bin/docker-compose
```

 

# docker私有仓库

## 搭建私有仓库

```shell
# 1. 拉取私有仓库镜像
docker pull registry
# 2. 启动私有仓库容器
docker run -id --name=registry -p 5000:5000 registry
# 3. 在浏览器输入 http://私服ip:5000/v2/_catalog, 看到{“repositories”:[]}，表示私有仓库搭建成#    功
# 4. 修改daemon.json
vim /etc/docker/daemon.json
# 在该文件中添加一个key，保存退出。此步让docker信任私有仓库；注意将私有仓库服务器ip修改为自己私有服务# 器真实ip
{"insecure-registries":["私有仓库服务器ip:5000"]}
# 5. 重启docker服务
systemctl restart docker
docker start registry
```



## 上传镜像到私有仓库

```shell
# 1. 标记镜像为私有仓库镜像
docker tag 镜像名:版本号 私有仓库ip:5000/镜像名:版本号
# 2. 上传已标记镜像
docker push 私有仓库ip:5000/镜像:版本号
```



## 从私有仓库拉取镜像

```shell
docker pull 私有仓库ip:5000/镜像名:版本号
```

































