其余链接：[环境准备](Preparation.md)     [SpringBoot](SpringBoot.md)     [Java8](Java8.md)     [Mybatis整合](SpringBoot-Mybatis)

[TOC]

笔记工具：Typora书写+Git管理更新

系统：Surface安装Ubuntu+Windows双系统 

# 快捷键查询

## Git

参考：[廖雪峰Git教程](https://www.liaoxuefeng.com/wiki/896043488029600/896827951938304)

原理简述:

1. 工作区，暂存区和仓库：先添加(add)到暂存区；把暂存区内容全部提交(commit)到当前分支

2. 版本控制指针：HEAD为版本指向指针，能用于撤回版本等操作
3. 分支管理：
   1. Fast forward模式：指针指向，快速合并分支，删除分支后会丢失被删除分支的信息
   2. --no-ff模式： 会创建一个commit，删除后能查找到分支的历史

### 更新提交操作

1. git status : 查看当前仓库状态
2. git add A : 添加文件A
3. git add . ：添加所有文件
4. git commit -m "message" ： 提交到本地仓库
5. git push ： 提交到远程服务器上

### 撤销操作

1. git checkout -- file : 丢弃当前修改（未添加到暂存区的部分）
2. git reset HEAD file :  把暂存区的内容撤回到工作区，HEAD表示最新版本
3. commit后的撤销，即回退版本操作

### 分支管理

1. 创建并切换分支：git checkout -b <name>  / git swithc -c <name> 
2. 创建并切换分支：
   1. git branch <name> 
   2. git checkout  <name> 
3. 查看当前分支：git branch
4. 合并指定分支到当前分支：
   1. 合并：git merge <name> 
   2. 无ff模式：git merge --no-ff -m "message" <name> 
5. 删除分支：git branch -d <name> 
6. 查看分支合并图：git log --graph --pretty=oneline --abbrev-commit
7. 强行删除一个未合并的分支：git branch -D <name>

### 远程仓库

1. 查看远程库信息：git remote (-v)
2. 推送分支(远程 本地): git push origin <name> 
3. 从远程origin/dev上，创建本地dev分支：
   1. git checkout -b dev origin/dev
   2. git push origin dev
4. 提交远程发生冲突：先pull拉取远程分支，本地解决冲突，再push推送到远程仓库

### 应用：如何把Github的项目上传到Gitlab上

从Git原理的学习得知，本地仓库和远程仓库可以通过push和pull连接。

因此目前状况是：本地仓库默认和远程Github仓库连接。

删除和Github的远程分支关联关系：git remote rm origin

添加Gitlab的ssh连接（在Gitlab上新建一个空项目）：git remote add origin

查看远程分支：git remote -v

上传本地项目到远程Gitlab：git push --set-upstream origin master

## Linux

复制文件：

> cp /home/a.tar.gz /usr/local/lib

解压压缩包：（z: gzip，v:显示所有过程，x:解压，f:必须有，放置最后，使用档案名称的意思，即后面接文件名称）

> tar -zvxf jdk.tar.gz

编辑文件：

> vim a.txt

重启电脑：（关机是-h，停机halt的意思；-k不关机，只是对所有在线用户发送警告；-c取消已经在进行的shutdown）

> shutdown -r now 

查看进程：

> ps -l  #查看自己的进程
>
> ps aux  #查看系统所有的进程
>
> ps aux|grep threadx #查看特定进程
>
> pstree -A #查看所有进程树

查看端口占用

> netstat -anp | grep port

## IDEA

删除行

> Ctrl + X

复制行

> Ctrl + D

格式化代码

> Ctrl + Alt + L

生成get/set实现接口

> ALT + INSERT

显示方法参数信息

> Ctrl + P

显示类结构图

> Ctrl + H

导入包自动修正

>  Alt + 回车



# 







![Be happy~](pictures/happy.jpg)



