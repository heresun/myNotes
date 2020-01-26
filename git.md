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

## 工作区和暂存区

> git 和其他版本控制系统不通知去就是有暂存区的概念
>
> + **工作区：**工作区就是运行`git init`的文件夹
>
> + **版本库：** 工作区有个隐藏的`.git`文件夹，它就是版本库
>
> + **暂存区：** git  的版本库里存了很多东西，最重要的是成为**stage**（或**index**）的暂存区，还有git自动创建的第一个分支**master**，以及指向`master`的指针`HEAD`
>
>   ![git-repo](https://www.liaoxuefeng.com/files/attachments/919020037470528/0)
>
> 

## 创建仓库

> 1. 新建一个文件夹
> 2. 进入文件夹
> 3. 执行`git init`

## 提交新文件到库中

> `git add 文件`：将文件从工作区添加到暂存区
>
> `git commit -m "说明"`：将暂存区的所有内容提交到当前分支
>
> **即需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。**
>
> 可以使用`git status`查看当前仓库的文件状态

## 提交修改文件到库中

> `git add 文件`
>
> `git commit -m "说明"`
>
> `git diff`：查看暂存区和工作区文件的区别
>
> `git diff HEAD -- test.txt` ：查看未提交的文件（包括已经`add`的修改和未`add`的修改）和版本库里最新版本的区别 

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
> > + `git reset --hard HEAD^` 该命令返回上一个版本如果是两个`^`则返回上上一个版本，如果是`~100`则返回100版本前
> >
> > + `git reset --hard commit_id` 根据提交id返回到指定的版本，版本号不用写全，写前五位就可以
> >
> > + `git reflog` 用于查看命令历史，使得无论什么时候都可以查询到库的版本信息
> >
> >   ![image-20200124143924026](C:\Users\14402\AppData\Roaming\Typora\typora-user-images\image-20200124143924026.png)

## 管理修改

git之所以更优秀是因为它跟踪的是**修改**而不是**文件**

### 什么是修改？

> 添加新文件，新增若干行，删除若干行，修改若干个字符

### 添加修改

> 每次修改，如果不用`git add`到暂存区，那就不会加入到`commit`中。

### 撤销修改

> `git checkout -- test.txt` ：把 test.txt 文件在工作区的修改全部撤销：
>
> + 情况一：test.txt自修改后还未add到暂存区，现在撤销修改回到和版本库一模一样的状态
> + 情况二：test.txt已经添加到暂存区，又作了修改，现在撤销修改回到添加到暂存区后的状态
>
> **总之就是让这个文件回到最后一次`git commit`或者`git add`时的状态**