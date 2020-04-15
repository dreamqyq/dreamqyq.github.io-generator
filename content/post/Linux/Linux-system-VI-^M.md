---
title: "Linux系统中VI下莫名出现的^M"
date: 2018-05-13
draft: false
toc: false
tags: ["Linux"]
categories: ["Linux"] 
---

本文写自我从windows转到Linux系统下，各种找bug的日常
#### 首先就是我在windows下写的运行很正常的bash脚本和node脚本，在Linux的终端里用vim运行，各种报错。

- 比如运行这一句：`#! /usr/bin/env node`旨在让node文件可以直接默认用node运行，而不用`node fileName`，然后就出现了如下报错

![](https://upload-images.jianshu.io/upload_images/11827773-c74cff58b8f7bcef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

起初我以为是这句代码本身的问题或者Linux中node安装的有问题，Google出来的结果，很多回答的意思是这样的，
>Linux系统安装node的时候，为了防止和系统本身的node文件重名，所以该系统安装的node的名称是nodejs

所以最初的解决方案是把node与nodejs建立软连接
`sudo In -s /usr/bin/nodejs /usr/bin/node`
然后就报了这样的错误
![](https://upload-images.jianshu.io/upload_images/11827773-8d1f5ac59846fba6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
事实证明，`/usr/bin/env: "node\r" 没有那个文件或目录`的问题不是出在这里
后来突然发现一件事，上面的解决方案，解决的错误都是`/usr/bin/env: "node" 没有那个文件或目录`
注意，两个错误最大的不一样&nbsp;&nbsp;&nbsp;__\r__
所以真正的问题是，windows下写的文件，最后一行的换行使用<kbd>Enter</kbd>,
所以每一行末尾都有一个莫名出现的**^M**

###该问题的解决方案
在Linux下的终端中，用vim运行曾经写的脚本文件
然后运行:`%s/^M//g`

__注意__：

__上述命令行中的“ ^M ”符，不是“ ^ ”再加上“ M ”，__
__而是由<kbd>Ctrl</kbd>+<kbd>v</kbd> 、<kbd>Ctrl</kbd>+<kbd>M</kbd>键生成的，或者<kbd>Ctrl</kbd>+<kbd>v</kbd>，再按回车<kbd>Enter</kbd>。__

### 以下是出现该问题的原因:
>很久以前，老式的电传打字机使用两个字符来另起新行。一个字符把滑动架移回首位 (称为回车，`<CR>`，ASCII码为0D)，另一个字符把纸上移一行 (称为换行, `<LF>`，ASCII码为0A)。当计算机问世以后，存储器曾经非常昂贵。有些人就认定没必要用两个字符来表示行尾。UNIX 开发者决定他们可以用 一个字符来表示行尾，Linux沿袭Unix，也是`<LF>`。Apple 开发者规定了用`<CR>`。开发 MS-DOS以及Windows 的那些家伙则决定沿用老式的`<CR><LF>`。



- 三种行尾格式如下:
unix : n
dos : rn
mac : r

这意味着，如果你试图把一个文件从一种系统移到另一种系统，那么你就有换行符方面的麻烦。
因为MS-DOS及Windows是回车＋换行来表示换行，因此在Linux下用Vim查看在Windows下写的代码，行尾后“^M”符号。
比如这样：![正是因为这个原因导致了我纠结了将近一天的bug](https://upload-images.jianshu.io/upload_images/11827773-0ebc666a5eb7eac6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 同时因为这个问题，所学到的vim的一些知识
- vim -b fileName 以二进制模式运行文件，可以看到行末的^M，就如上图一样
- vim中的替换命令
输入 `:s/thee/the `<kbd>Enter</kbd> ，将__thee__替换成__the__。  请注意该命令只改变光标所在行的第一个匹配串。
输入   `:s/thee/the/g `则是替换全行的匹配串，该行中所有的 "thee" 都会被改变。
输入   `:%s/old/new/g `    则是替换整个文件中的每个匹配串。
输入   `:%s/old/new/gc`    会找到整个文件中的每个匹配串，并且对每个匹配串提示是否进行替换。
所以在上面的解决方案中，`%s/^M//g`这行代码的意思是把整个文件中出现的^M替换成空

- vim中，在文件内查询特定字符串
在正常模式下输入 / 字符。您此时会注意到该字符和光标都会出现在屏幕底部，这跟 : 命令是一样的。
按<kbd>Enter</kbd>进行查询；
按<kbd>N</kbd>,再次查找和上次同样的字符串
- vim中匹配括号，当光标移动到一个 (、)、[、]、{ 或 } 处，使用<kbd>%</kbd>匹配其所对应的另一个括号
