---
title: git指令总结

date: 2019-06-30


---

# git基本设置

```
git config --global user.name "panyexiong"
git config --global user.email "289573907@qq.com"
ssh-keygen -t rsa -C "289573907@qq.com"
```

# git拉取仓库

```
git clone git@github.com:panyexiong/LearningGit.git
```

### 查看修改的文件

```
git diff
```

### 添加到本地暂存区

```
git add []
.	不加参数默认为将修改操作的文件和未跟踪新添加的文件添加到git系统的暂存区，注意不包括删除
-u	表示将已跟踪文件中的修改和删除的文件添加到暂存区，不包括新增加的文件，注意这些被删除的文件被加入到暂		存区再被提交并推送到服务器的版本库之后这个文件就会从git系统中消失了。
-A	表示将所有的已跟踪的文件的修改与删除和新增的未跟踪的文件都添加到暂存区
```

### 提交到本地版本库

```
git commit -m "message"
//-m 参数表示可以直接输入后面的“message”，如果不加 -m参数，那么是不能直接输入message的，而是会调用一个编辑器一般是
git commit -am "message" -am等同于-a -m
-a参数可以将所有已跟踪文件中的执行修改或删除操作的文件都提交到本地仓库，即使它们没有经过git add添加到暂存区，注意: 新加的文件（即没有被git系统管理的文件）是不能被提交到本地仓库的。
```

### 提交到远端仓库

```
git push
```

将本地master退送到远端dev(如果远端没有dev分支，将会新建)

```
git push origin master:dev
```



### 创建远端分支

- 新建本地分支dev,并切换至dev

  ```
  git checkout -b dev
  ```

- 将本地分支push到远端

  ```
  git push origin dev:dev
  ```

### 删除远端分支

1. 第一种推送一个空的分支到远端

   ```
   git push origin  :dev
   ```

2. 第二种

   ```
   git push origin --delete dev
   //删除本地分支
   git branch -d dev
   ```

### 切换分支

- 从本地master分支切换到远端dev分支

  ```
  //先从远端拉取分支dev
  git fetch origin dev
  //切换分支
  git checkout dev
  ```

- 目前在本地master分支，提交到远端dev分支

  ```
  git push origin master:dev
  
  ```

### 撤销操作

- 看chrome收藏的blog

  网址：https://blog.csdn.net/yxlshk/article/details/79944535