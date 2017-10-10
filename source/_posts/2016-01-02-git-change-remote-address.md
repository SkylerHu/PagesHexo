---
title: git仓库迁移
date: 2016-01-02 16:11
tags:
  - git
---

已有项目名为`trainingHub`

新的gitlab服务器地址`github.com`

新的gitlab服务中的User为`Skyler`


### 1. 第一步
在自己的gitlab后台建立一个新的项目，其地址为`git@github.com:Skyler/trainingHub.git`

### 2. 第二步
进入本地的`trainingHub`目录，然后执行下面两个命令：

``` bash
git remote remove origin
git remote add origin git@github.com:Skyler/trainingHub.git
```

### 3. 最后直接`pull`、`push`

``` bash
git remote remove origin
git remote add origin git@gitlab.pookor.com:Skyler/trainingHub.git
```

##### 若是有多个分支需要迁移

**git remote add `branch_name` git@gitlab.pookor.com:Skyler/trainingHub.git**

##### push所有分支

`git push --all origin`


### 注意
可强制覆盖远程服务器

`git push --all --force origin`
