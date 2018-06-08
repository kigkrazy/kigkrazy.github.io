---
title: 史上最简单的repo使用指南
categories:
 - android
tags:
 - Android
 - aosp
---

## 序
本篇文章将向大家介绍`repo`的简单使用，以及如何使用`repo`对`AOSP`庞杂的代码进行简单版本管理。

## `repo`简介
repo是Google开发的用于管理Android版本库的一个工具。repo并不是用来取代Git，而是用Python对Git进行了一定的封装，简化了对多个Git版本库的管理。对应repo管理的任何一个版本库，都需要使用Git命令进行操作。

### 如何使用`repo`
Repo 的使用形式如下：
```
repo <COMMAND> <OPTIONS>
```

可选元素显示在方括号[]里面。例如，许多命令接受的项目列表作为参数。您可以指定项目列表作为名称列表或本地源目录的路径列表：
```
repo sync [<PROJECT0> <PROJECT1> <PROJECTN>]
repo sync [</PATH/TO/PROJECT0> ... </PATH/TO/PROJECTN>]
```

### 初始化
```
repo init -u <URL> [<OPTIONS>]
```
在当前目录下安装 Repo。这会产生一个 `.repo/` 目录，目录包括装 Repo 源代码和标准 Android 清单文件的 Git 仓库。`.repo/` 目录还包括`manifest.xml`，是一个在`.repo/manifests/`目录选择清单的符号链接。

选项：
* -u：指定一个恢复清单仓库的地址（URL）。常见的清单可以在 https://android.googlesource.com/platform/manifest 找到。
* -m：在仓库里选择一个清单文件。如果没有清单名称，那么默认是 default.xml。
* -b：指定一个修正，例如，一个特殊的清单分支（manifest-branch）。

### 同步
```
repo sync [<PROJECT_LIST>]
```
下载新的更改，更新在你本地环境中的工作文件。如果你不带参数运行`repo sync`，它将同步所有项目的文件。

### 上传
```
repo upload [<PROJECT_LIST>]
```
在指定的项目中，Repo 把本地分支的更新比作远程分支在最后一次 Repo 同步。Repo 会提示你选择一个或更多尚未上传审查的分支。

### 差异
repo diff [<PROJECT_LIST>]
在 commit 和工作目录之间使用 git diff 显示明显差异的更改。

### forall
```
repo forall [<PROJECT_LIST>] -c <COMMAND>
```
在每个项目中被给予的 shell 命令。如下的附加环境变量是通过 repo forall 才变得有效的：

* REPO_PROJECT 设置项目唯一的名称。
* REPO_PATH 是相对于客户端 root 的路径。
* REPO_REMOTE 是清单中远程系统的名称。
* REPO_LREV 是清单中修订本的名字，翻译成一个本地跟踪分支。如果你需要通过清单修正去本地执行 git 命令的时候可以使用。
* REPO_RREV 是清单中修订本的名字，正如在清单中所写的那样。

选项：

* -c：执行命令和参数。命令是通过 /bin/sh 评估的并且后面的任何参数就如 shell 位置的参数通过。
* -p：在指定命令的输出前显示项目标题。这是通过绑定管道到命令的stdin，stdout，和 sterr 流，并且用管道输送所有输出量到一个连续的流，显示在一个单一的页面调度会话中。
* -v：显示命令写到 sterr 的信息。

## 简单的本地化管理
一般我们修改完源码后，使用`repo forall [<PROJECT_LIST>] -c git commit -m "xxx"`命令对代码进行提交

##　参考

[Android 开源项目指南](http://wiki.jikexueyuan.com/project/android-source/using-repo.html)