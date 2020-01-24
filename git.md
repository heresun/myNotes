# GIT

## git的安装

windows

> 直接官网下载

linux

> ```shell
> sudo apt-get/yum install git
> ```
>
> 从源码安装
>
> `./config`
>
> `make`
>
> `sudo make install`

## 创建仓库

> 1. 新建一个文件夹
> 2. 进入文件夹
> 3. 执行`git init`

## 提交新文件到库中

> `git add 文件`
>
> `git commit -m "说明"`
>
> 可以使用`git status`查看当前仓库的文件状态

## 提交修改文件到库中

> `git add 文件`
>
> `git commit -m "说明"`
>
> 在提交之前可以使用 `git diff`查看文件的修改情况  

## 版本回退

> `git log` 查询提交的历史记录，其结果为从按时间从近到远显示，结果如下：
>
> ![image-20200124141306683](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200124141306683.png)
>
> 可以加上`--pretty=oneline`参数，使得结果在一行
>
> ![image-20200124141529030](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200124141529030.png)
>
> > 图中一串十六进制的数字是 commit id，标志每一次提交
>
> **版本回退操作方法**
>
> > `git reset --hard HEAD^` 该命令返回上一个版本
> >
> > 如果是两个`^`则返回上上一个版本，如果是`~100`则返回100版本前
> >
> > `git reset --hard commit_id` 根据提交id返回到指定的版本

