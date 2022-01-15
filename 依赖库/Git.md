> 流程

```tex
1.分布式版本控制工具和集中式版本控制工具
2.Git安装  https://npm.taobao.org/mirrors/git-for-windows/  淘宝镜像
3.Git命令
4.Git分支 分支特性 分支创建 分支转换 分支合并 代码合并冲突
5.idea 集成Git

=============  Github  ============
创建远程库
代码推送  push
代码拉取  pull
代码克隆  clone
SSH免密登录
idea集成github

==============  Gitee  ============
码云创建远程库
idea集成 gitee
码云连接github进行代码迁移和复制

============== GitLab  =============
GitLab服务器搭建和部署
idea集成gitlab
```

# Git

> 简介

```tex
开源免费的分布式版本控制管理工具，性能好
是linux开发者开发的，可以使用linux命令
```

## 版本控制工具

### 集中式版本控制工具

```tex
例如SVN  CVS
单一的集中管理的服务器，保存所有文件的修订版本，协同工作的人都从这一台服务器拉取或提交数据
好处:每个人都可以在一定程度上看到其他人在做什么，管理员也可以轻松的控制开发者的权限，维护方便
坏处:最常见的是服务器单点故障，导致无法拉取和提交
```

### 分布式版本控制工具

```tex
例如git 
每次不只是提取最新版本的文件快照，而是克隆完整的仓库镜像
优点 
	-服务器断网的情况下也可以进行开发(本地的版本控制)
	-每个客户端保存的也都是整个完整的项目(包含历史记录，更加安全)
```

## 工作机制

![image-20210825101318655](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825101318655.png)

## 安装

```tex
不选配置变量，其他默认
```

## Git常用命令

```bash
#设置用户签名
	git config --global user.name 用户名
#设置用户签名
	git config --global user.email 邮箱
#初始化本地库
	git init
#查看本地库状态
	git status
#添加到暂存区
	git add 文件名
#提交到本地库
	git commit -m"日志信息" 文件名
#查看历史纪录
	git reflog
#版本穿梭
	git reset --hard 版本号
```

> 设置用户签名

```bash
#设置用户签名，设置后可以在C盘本地用户下查看gitconfig文件
	git config --global user.name 用户名
	git config --global user.email 邮箱
## 用来说明提交的用户信息，不设置提交时会报错
```

> 初始化本地库

```bash
#初始化本地库
	git init
	
#在需要初始化的库中右键进入git bash
```

> 查看本地库状态

```bash
#查看本地库状态
	git status
##################
On branch master
No commits yet
nothing to commit (create/copy files and use "git add" to track)
###################
On branch master
No commits yet
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        hello.txt
nothing added to commit but untracked files present (use "git add" to track)
###########################
vim 操作 yy复制 p粘贴
vim hello.txt  #新建文件的状态是红色
```

> 添加暂存区

```bash
#添加到暂存区，文件状态变绿
	git add 文件名
###############
warning: LF will be replaced by CRLF in hello.txt.  #自动转换换行符 windows转linux
The file will have its original line endings in your working directory

#删除暂存区文件
	git rm --cached 文件名
```

> 提交本地库

```bash
#提交到本地库
	git commit -m"日志信息" 文件名
	
##########################################
git commit -m "first commit from zwj" hello.txt
    warning: LF will be replaced by CRLF in hello.txt.
    The file will have its original line endings in your working directory
    [master (root-commit) 8374c73] first commit from zwj
     1 file changed, 16 insertions(+)
     create mode 100644 hello.txt

```

> 查看历史记录

```bash
#查看历史纪录
	git reflog
###############################
git reflog
	8374c73 (HEAD -> master) HEAD@{0}: commit (initial): first commit from zwj
	
#详细记录
	git log
###################################
git log
commit 8374c736b3e4caeb552bdab9e3e944adadd187f6 (HEAD -> master)
Author: zwj <1628725633@qq.com>
Date:   Wed Aug 25 11:52:49 2021 +0800

    first commit from zwj
```

> 版本穿梭

```bash
#版本穿梭,版本号为 log日志中的commit版本随机号
	git reset --hard 版本号
	
#版本穿梭后，本地库内容也会回到选定的版本,通过head指针进行版本穿梭
```

![image-20210825121425724](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825121425724.png)

## Git分支操作

![image-20210825121503910](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825121503910.png)

### 什么是分支

```tex
在版本控制过程中，同时推进多个任务，每个任务都可以创建独立分支，将开发工作从主线上分离下来，开发自己分支时不会影响主线分支，
底层是利用指针
```

> 优点

```tex
同时并行推进多个功能开发，提高效率
各个分支在开发过程中不会对其他分支造成影响，如果一个分支开发失败，删除重写即可
```

![image-20210825122535767](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825122535767.png)

### 分支的操作

```bash
#创建分支
	git branch 分支名
#查看分支
	git branch -v
#切换分支
	git checkout 分支名
#把指定的分支合并到当前分支上
	git merge 分支名
```

> 合并分支(冲突合并)    master|MERGING状态下

```bash
#产生原因:两个分支在同一个文件都做了修改，git无法决定采用哪一个分支的，需要人为决定用哪一个
    git merge hot-fix
        Auto-merging hello.txt
        CONFLICT (content): Merge conflict in hello.txt
        Automatic merge failed; fix conflicts and then commit the result.
#发生冲突手动打开文件进行修改

	vim hello.txt
		<<<<<<< HEAD
        hello world!  hello beautiful girl master modify
        hello world!  hello beautiful girl
        =======
        hello world!  hello beautiful girl
        hello world!  hello beautiful girl hot-fix modify
        >>>>>>> hot-fix
#修改后再add 提交       
	git add hello.txt
    git commit -m "master merge"    # master|MERGING状态下，不用带hello.txt
    [master 5971719] master merge
```

## 团队协作

### 团队内协作

![image-20210825144824155](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825144824155.png)

### 跨团队协作

![image-20210825145838618](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825145838618.png)

## 远程仓库操作

```bash
#查看当前所有远程地址别名
	git remote -v
#创建远程地址别名
	git remote add 别名 远程地址
#推送本地分支上的内容到远程仓库
	git push 别名 分支
#将远程仓库的内容克隆到本地
	git clone 远程地址       #clone会做的操作  拉取代码，初始化本地仓库 创建别名origin
#将远程仓库对于分支的最新内容拉下来与本地分支合并
	git pull 远程库地址别名 远程库分支名
```

### 远程加入团队

#### 选择邀请合作者

![image-20210825155855003](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825155855003.png)

```bash
## 团队成员通过自己的github账号push项目到团队的总帐号
## 团队的成员中需要声明成员账号,声明后复制邀请函链接地址,在成员的github账号登陆后粘贴链接地址接受邀请
	git push 团队总账号地址 分支名
```

#### 跨团队合作

![image-20210825165029515](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825165029515.png)

![image-20210825165052539](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825165052539.png)

> =====================================================================================================

![image-20210825165716766](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825165716766.png)

![image-20210825165429608](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825165429608.png)

![image-20210825165630338](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825165630338.png)

## SSH免密登录

![image-20210825170743732](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825170743732.png)

```bash
#生成SSH密码 rsa加密算法 在C:\Users\zwj中打开git
	ssh-keygen -t rsa -C 1628725633@qq.com
```

![image-20210825171455253](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825171455253.png)

![image-20210825171539209](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825171539209.png)

![image-20210825171611527](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825171611527.png)

![image-20210825172820776](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825172820776.png)

![image-20210825172846357](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825172846357.png)

## idea集成git

### 配置Git忽略文件

```tex
将.idea文件夹中的文件和.imi文件屏蔽，因为它们不参与程序运行，是IDE中的东西
```

> 怎么忽略

```tex
-创建git.ignore文件在用户目录下  zwj/

-gitconfig中引用
    [core]
        excludesfile = C:/Users/zwj/git.ignore   #正斜线
```

> 常用配置内容

```bash
# Compiled class file
*.class

# Eclipse
.project
.classpath
.settings/

# Intellij
*.ipr
*.iml
*.iws
.idea/

# Maven
target/

# Gradle
build
.gradle

# Log file
*.log
log/

# out
**/out/

# Mac
.DS_Store

# others
*.jar
*.war
*.zip
*.tar
*.tar.gz
*.pid
*.orig
temp/
```

### idea定位git.exe位置

![image-20210825174438209](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825174438209.png)

### 初始化

![image-20210825174739348](C:\Users\zwj\AppData\Roaming\Typora\typora-user-images\image-20210825174739348.png)