---
title: Git常用操作命令
tags:
  - Git
categories:
  - 开发环境
date: 2018-04-09 00:00:00
---


## 前言

Git是分布式版本控制系统，平时工作中一直在使用的。整理一下常用的一些操作命令。

## 创建版本库

在项目根目录下，运行 *`git init`* 命令初始化一个Git仓库。
``` bash
  git init
```
<!-- more -->
## 文件操作

### 添加文件到Git仓库
1. 首先用`git add <file>`命令把**工作区**的文件添加到**暂存区(stage)**
``` bash
  git add readme.md
```
2. 用`git commit`命令提交更改的文件，就是把暂存区的内容提交到*当前分支*。`-m`后面是更改描述，建议写上，便于协作。
```
  git commit -m '新增readme.md文件'
```
可以用`git status`命令查看本地仓库的文件状态。
***
**工作区：**本地电脑的项目文件夹。
**暂存区：**工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的*暂存区*，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。
{% img /images/2018-04-09/git.jpeg 图片来自廖雪峰老师博客%}

### 版本回退
`git log`命令显示从最近到最远的提交日志。  `--pretty=oneline`简化显示
```
  git log --pretty=oneline
```
用`git log --graph`命令可以看到分支合并图

通过 git log 可以看到每个版本的commit_id。用 `git reset --hard commit_id`可以指定回退到哪个版本。`HEAD`表示当前版本，上一版本为`HEAD^`。
```
  git reset --hard HEAD^
```
版本回退后，可以用`git reflog`命令查看命令历史，以便可以回到未来的版本。

### 撤消修改
通过上面已经知道，本地仓库有_工作区_和_暂存区_两个状态，撤消不同状态的已经修改的文件，也有区分：
1. 直接丢弃工作区的修改的文件时，用命令`git checkout -- file`。 `--`非常重要。git checkout其实是用版本库里的版本替换工作区的版本
    ```
    git checkout -- file
    ```
2. 如果工作区修改的文件通过 `git add`提交到了暂存区，要丢弃修改，需要先用命令 `git reset HEAD file`把暂存区的修改撤销掉（unstage），然后再继续上面1的步骤丢弃工作区修改。
    ```
    git reset HEAD file
    git checkout -- fiile
    ```

### 删除文件
删除版本库中的文件，先`git rm file`，然后提交 `git commit`
```
  git rm file
  git commit -m 'remove file'
```

### 远程仓库
#### 添加远程仓库
* 本地仓库要关联到远程仓库，使用命令 `git remote add origin git@server-name:path/repo-name.git`。
* 关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容。
* 此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改。

#### 从远程库克隆
从远程仓库克隆使用`git clone`命令
```
  git clone git@server-name:path/repo-name.git
```

### 分支管理
查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`

创建+切换分支：`git checkout -b <name>`

合并某分支到当前分支：`git merge <name>` 。 

```
  git merge --no-ff -m "merge with no-ff" dev
```
`--no-ff`: 强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

删除分支：`git branch -d <name>`。 
如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除(常用于临时的feature分支)

#### 其它
 `git stash` : 如果当前分支工作区有修改的文件不想提交，切换到其它分支。可以先把当前工作现场“储藏”起来，等以后恢复现场后继续工作
  ```
    git stash
  ```
`git stash pop`，回到工作现场
`git stash list`查看“储藏”的工作现场
有多个工作现场时，用 `git stash apply stash@{0}`指定恢复到哪一个工作现场。

### 标签管理
切换到需要打标签的分支后， 用 `git tag <name>`就可以打一个新标签。默认标签是打在最新提交的commit上的
```
  git checkout master
  git tag v1.0
```
给之前的历史提交打标签，通过`git log`查看commit_id。
```
  git tag v0.9 commit_id
```
带有说明的标签，用`-a`指定标签名，`-m`指定说明文字:
```
  git tag -a v0.1 -m "version 0.1 released" 3628164
```
`git tag`查看所有标签。

`git show <tagname>`查看标签信息