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