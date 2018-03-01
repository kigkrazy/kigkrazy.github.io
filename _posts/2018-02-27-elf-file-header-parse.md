---
title: Git仓库瘦身指南
categories:
 - git
tags:
 - git
 - git瘦身
 - git清除大文件
---

## 参考资料
[Git从库中移除已删除大文件](http://blog.csdn.net/zcf1002797280/article/details/50723783)  
[寻找并删除Git记录中的大文件](http://harttle.land/2016/03/22/purge-large-files-in-gitrepo.html)  
[Git从库中移除已删除大文件](https://rtyley.github.io/bfg-repo-cleaner/)  

## 查找大文件方法
```
git rev-list --objects --all | grep "$(git verify-pack -v .git/objects/pack/*.idx | sort -k 3 -n | tail -5 | awk '{print$1}')"
```

## 使用BFG删除GIT的大文件历史记录

BFG是一个清理GIT仓库大文件的利器。[BFG下载](https://rtyley.github.io/bfg-repo-cleaner/)

### 使用例子
* 清理超过`20M`大小的文件

```
java -jar bfg-1.13.0.jar --strip-blobs-bigger-than 20M ${仓库地址}
cd ${仓库地址}
git reflog expire --expire=now --all && git gc --prune=now --aggressive
```

* 清理文件名是`id_rsa`或者`id_dsa`的文件

```
java -jar bfg-1.13.0.jar --delete-files id_{dsa,rsa} ${仓库地址}
cd ${仓库地址}
git reflog expire --expire=now --all && git gc --prune=now --aggressive
```

* 清理文件名是`largefolder`的文件夹

```
java -jar bfg-1.13.0.jar --delete-folders largefolder ${仓库地址}
cd ${仓库地址}
git reflog expire --expire=now --all && git gc --prune=now --aggressive
```