---
title: "命令行初识（介绍常用命令）"
date: 2018-04-28
draft: false
categories: ["前端基础"] 
tags: ["命令行"]
---

## 1、ls命令 —— 显示当前目录下的路径
 该命令对应的单词是list ，其不加参数时不显示隐藏文件。中包含以下常用的参数说明：
 - `ls -a` 显示当前目录下的所有文件（包括以.开头的隐藏文件）
  - `ls -l`显示当前目录下文件的详细信息
  - `ls -al` 显示当前目录下的所有文件的详细信息
  - 注：详细信息中的字母含义（drwx）
    - d（第一个字母）——如果是目录则是d，如果是文件则是 
    - r——是否可读
    - w——是否可写
    - x——是否可执行
   
   
## 2、cat —— 该命令对应的单词是concatenate
一些参数说明(摘自runoob的相关解释)：
> -n ：由 1 开始对所有输出的行数编号。
-b ：和 -n 相似，只不过对于空白行不编号。 <br>
-s：当遇到有连续两行以上的空白行，就代换为一行的空白行。 <br>
-v ：使用 ^ 和 M- 符号，除了 LFD 和 TAB 之外。 <br>
-E  : 在每行结束处显示 $。 <br>
-T : 将 TAB 字符显示为 ^I。 <br>
-e : 等价于 -vE。 <br>
-A, --show-all：等价于 -vET。 <br>
-e：等价于"-vE"选项； <br>
-t：等价于"-vT"选项；

## 3、mv —— 对应单词move（移动或重命名）

常用用法：

- `mv 文件名 文件名`——将源文件名改为目标文件名
- `mv 文件名 目录名`——将文件移动到目标目录
- `mv 目录名 目录名`
  - ——目标目录已存在，将源目录移动到目标目录；
  - ——目标目录不存在则改名

## 4、touch —— 新建文件（不能添加文件内容）
用法：

`touch fileName`
——文件名不存在，则新建文件
——文件名存在则改变文件更新时间（文件内容不变）

## 5、rm —— remove 删除
-  `rm` 删除文件
- `rm -f` 强制删除文件
- `rm -r` 删除路径
- `rm -rf` 强制删除路径（包括其中的问价）

## 6、另外一些常用命令
- `cd` 即 change directory 进入/改变路径
- `mkdir` 即 make directory 创建路径
  - `mkdir -p demo1/demo2` 创建一个名为demo1的路径，里面还有一个demo2路径 
- `echo` 创建文件相关
- `cp` 即 copy 复制文件
- `tree` 查看当前路径的目录结构（windows平台不支持）
- `ln -s` 建立软连接：如`ln -s demo demo-link`
- 以默认程序打开文件
  - `start` windows平台
  - `open` mac OS 平台
  - `xdg-open` Linux 平台
- `pwd` 显示当前文件夹的绝对路径
- `whoami` 我的用户名

## 7、一个查询命令行的网站

网站地址：https://explainshell.com/explain?cmd=ls+-lrt
用法如下图所示：
![image.png](https://upload-images.jianshu.io/upload_images/11827773-c45190f45b4859e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
