---
title: "git 入门"
date: 2018-04-28
draft: false
categories: ["前端基础"] 
tags: ["git"]
---

**前言**：是一个分散式版本控制软件，最初由林纳斯·托瓦兹制作，于2005年以GPL协议出品。如[网站github](https://github.com/)，本文简单的介绍一些的git 的常用操作。

## 1、 `git init`

命令作用：在当前目录新建一个Git代码库
使用时间：当需要创建新仓库时使用
代码实例：`git init`

![](https://upload-images.jianshu.io/upload_images/11827773-5d65ee73b5885f38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 2、` git add`

命令作用：添加指定文件到暂存区
使用时间：当本地创建文件或更新文件时，需要现将其存入暂存区
代码实例：`git add index.html`

![](https://upload-images.jianshu.io/upload_images/11827773-c48879435da98583.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 3、`git commit -v`

命令作用：提交时显示所有diff信息 
代码实例：`git commit -v`

![](https://upload-images.jianshu.io/upload_images/11827773-34e6dfe939775af9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 4、 `git commit -m`

命令作用：提交暂存区到仓库区
使用时间：当所有的文件都创建或者更新完成并都提交至暂存区后使用
代码实例：` git commit -m "新的更新"`

![](https://upload-images.jianshu.io/upload_images/11827773-a5ed735e7ad74794.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 5、代码上传

- `git pull` 取回远程仓库的变化，并与本地分支合并
- `git push` 上传本地指定分支到远程仓库

## 6、一些简单的查询命令
- `git diff` 显示暂存区和工作区的差异
- `git status -sb` 显示有变更的文件
- `git log` 显示当前分支的版本历史

## 7、git中commit合并

大致流程：
```bash
git log |head
git rebase -i d1
# if fail, use git rebase --abort
git push --force
```
详细流程：
1. 首先：查看提交历史，`git log`,历史记录是按照时间排序的，时间近的排在前面。
2. `git rebase`。想要合并1-3条，有两个方法：
    - 从HEAD版本开始往过去数3个版本
`git rebase -i HEAD~3` 
    - 指名要合并的版本之前的版本号
`git rebase -i 3a4226b`
请注意3a4226b这个版本是不参与合并的，可以把它当做一个坐标

3. 选取要合并的提交
    - 执行了`rebase`命令之后，会弹出一个窗口，头几行如下：
    ```shell
    pick 3ca6ec3   '注释**********'
    pick 1b40566   '注释*********'
    pick 53f244a   '注释**********'
    ```
   - 将`pick`改为`squash`或者`s`,之后保存并关闭文本编辑窗口即可。改完之后文本内容如下：
    ```sh
    pick 3ca6ec3   '注释**********'
    s 1b40566   '注释*********'
    s 53f244a   '注释**********'
    ```
4. 然后保存退出，Git会压缩提交历史，如果有冲突，需要修改，修改的时候要注意，保留最新的历史，不然我们的修改就丢弃了。修改以后要记得敲下面的命令：
`git rebase --abort `

5. 如果没有冲突，或者冲突已经解决，则会出现如下的编辑窗口，输入提交信息后退出即可。
```bash
# This is a combination of 4 commits.  
#The first commit’s message is:  
注释......
# The 2nd commit’s message is:  
注释......
# The 3rd commit’s message is:  
注释......
# Please enter the commit message for your changes. Lines starting # with ‘#’ will be ignored, and an empty message aborts the commit.
```
6. 然后再次查看提交历史就会发现这几次提交已经被合并了，然后`git push -f`提交到远程即可。
    - 关于`git push -f`命令：
如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做`git pull`合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用`--force`选项。使用`--force`选项，结果导致远程主机上更新的版本被覆盖。除非你很确定要这样做，否则应该尽量避免使用`--force`选项。


## 10、一些关于git更深入的介绍
- [阮一峰关于git的介绍](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)
- http://markyun.github.io/2015/instruction-Git/
- [git工作流](https://github.com/xirong/my-git/blob/master/git-workflow-tutorial.md)



