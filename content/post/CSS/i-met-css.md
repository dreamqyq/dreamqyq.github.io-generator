---
title: "初识CSS"
date: 2018-05-18
draft: false
categories: ["CSS"] 
tags: ["CSS"]
---
> CSS，中文称之为“层叠样式表”，英文全称为 Cascading Style Sheets 。一种用来为结构化文档（如HTML文档或XML应用）添加样式（字体、间距和颜色等）的计算机语言，由W3C定义和维护。目前最广泛运用的版本是[CSS 2.1](https://www.w3.org/TR/2011/REC-CSS2-20110607/)(链接为CSS2.1的官方文档)。

## 1.  学习资源：

- __MDN__ => Google 关键词 + __MDN__ （用来查询css属性文档介绍）
- [CSS Tricks](https://css-tricks.com/) 可以学习很多css技巧
-  [阮一峰](http://www.ruanyifeng.com/blog/)、[张鑫旭](http://www.zhangxinxu.com/wordpress/category/css/)的相关css博客
- __codrops__ => Goole 关键词 + __codrops__ （学习css做出的特效）
- 《CSS揭秘》
-  CSS2.1 中文[官方文档](http://www.ayqy.net/doc/css2-1/cover.html)

## 2. CSS外部导入方式
` <link rel="stylesheet" href="XX"> `
` @import url(XXX) `
## 3. 浮动清除
子元素浮动，给父元素添加clearfix类
```
.clearfix::after{
	content:"";
	display:block;
	clear:both;
}
```
## 4.  文档流
-  概念：文档内元素的流动方向。
-  内联元素从左往右流动，若遇到阻碍，换行继续流动
- 块级元素，每一块占一行，从上往下依次流动
- 注：单词换行，浏览器默认word-break属性为“break-word”（按单词换行），即如果一个很长的英文单词中间没有空格，单词超出窗口宽度时，会出现滚动条而不会自动换行。设置 ` word-break : break-all ` 则按照字母换行。

## 5. CSS中的高度

- 块级元素如div的高度是由其内部文档流的高度的总和决定的。
- 一般情况下，span的高度是其内部文字字体的建议行高
![](https://upload-images.jianshu.io/upload_images/11827773-497a4081a35d4c9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 
## ６. 用CSS画一个三角形

- 直角三角形
```
div{
	border:30px solid transparent;
	width:0;
	border-left-color:red;
	border-top-width:0;
}
```
![直角三角形](https://upload-images.jianshu.io/upload_images/11827773-5b2e95cfe0a4dc40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 等腰三角形
```
div{
	border:30px solid transparent;
	width:0;
	border-left-color:red;
	border-right-width:0;
}
```
![等腰三角形](https://upload-images.jianshu.io/upload_images/11827773-9931680f73ff9461.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
