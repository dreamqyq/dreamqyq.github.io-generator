---
title: "元素的高度与宽度以及衍生技巧"
date: 2018-06-01
draft: false
categories: ["CSS"] 
tags: ["CSS"]
---


前言：本文主要写块级元素与内联元素的宽度与高度的相关知识，以及相关的衍生技巧，包括一些常用的居中方法等（与我的上一篇博客[css如何居中](https://www.jianshu.com/p/ed4cf187c4f0)不同，上一篇是把所有能用来居中的方法都介绍了一遍，本篇只写常用的方法，可以应对80%的情况）

## 1、块级元素宽高

- **块级元素的高度由其内部文档流的高度的总和决定的**
- **块级元素的宽度**：默认占一整行即`width:100%;` 另外，不论width设置的有多窄，一个块级元素永远都会**占据一整行**的空间
- 注：在非必要情况下，尽量不要设置height属性，要撑起所需的高度，可以设置padding或line-height等，不仅不容易出现bug，而且容易设置垂直居中。

## 2、内联元素

- **内联元素的高度**由其内部文字字体的**建议行高**决定
-  **内敛元素的宽度**由其内容的宽度决定
- margin属性padding属性会影响内联元素的宽度但不影响其高度，border属性影响其高度宽度
- 不论`display:inline` 还是`display:inline-block`，编辑html时两个内联元素之间有一个或多个空格、回车，在显示的时候都会自动转换为一个空格。

## 3、文档流或译为常规流（normal flow）
- 文档流：对文档的读取和输出的顺序. 其遵循内联元素从左到右,块级元素从上到下的读取, 输出和显示顺序；

以下设置会脱离文档流：
- 绝对定位 `position:absolute；`
- 固定定位`positon:fixed`
- 浮动 `float`

## 4、margin合并
- 行内元素左右margin不合并
- 块级元素上下margin合并，合并后的margin为二者的最大值
- 浮动元素margin不合并
- 同级元素清除margin合并：
（1）二者之间提添加一个边框宽度为0.1的div
（2）二者设置`display:flex;`
（3）二者设置`display:table`
- 父子元素的margin合并
如图所示，子元素设置margin，父元素高度没有发生变化，导致看似是父元素有了margin，其实是margin合并了：
![](https://upload-images.jianshu.io/upload_images/11827773-6d2bb7e3553c5bf0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)若要清除父子元素的margin合并，为父元素设置`border`、 `padding` 或`overflow:hidden`（overflow不推荐使用）

## 5、常用的水平居中方法

### （1）块级元素水平居中：
- 子元素不定宽：为子元素设置`margin: auto 20px;` （左右外边距相等即可）
- 子元素定宽：为子元素设置`margin: 0 auto;`

### （2）内联元素水平居中
- 为父元素设置`text-align:center;`

## 6、常用的垂直居中方法

### （1）父元素高度不确定
- 不论子元素高度是否确定，都可以给父元素上下相同的`padding`即可 

### （2）父元素高度确定 
- 单行文字垂直居中可以让父元素`line-height` = `height`
- IE或比较古老的移动端设备：table布局
```
.parent{
  display:table;
  height:100px;
  border:1px solid red;
}
.child{
  display:table-cell;
  vertical-align:middle;
  text-align:center;
}
```
- chrome及移动端：父元素flex布局
```
.parent{
      display:flex;
      justify-content:center;
      align-items:center;
}
```
- 父元素定高，子元素也固定宽高，也可以用绝对定位来居中
```
.parent{ 
  height:300px;
  position:relative;
  border:1px solid red;
}
.child{
  position:absolute;
  top:0;
  left:0;
  bottom:0;
  right:0;
  margin:auto;
  width:100px;
  height:200px;
  border:1px solid green;
}
```

## 7、溢出省略效果

- 单行溢出省略效果
```
div{
  border:1px solid red; 
  width:100px;
  text-overflow:ellipsis; /*文字超出部分用省略号代替*/
  white-space:nowrap; /*不能换行*/
  overflow:hidden; /*超出部分隐藏*/
}
```
效果图，原文字是this is demo and so on。超过div宽度即100px的部分用…代替：
![效果图](https://upload-images.jianshu.io/upload_images/11827773-9055c0ae84ea1130.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 多行溢出省略效果
**注：下面展示的样式设置对IE以及火狐浏览器不兼容，谨慎使用**
```
div{
  border:1px solid red;
  width:200px;
  display:-webkit-box;
  -webkit-line-clamp:3; /*只显示三行。溢出部分用省略号代替*/
  -webkit-box-orient:vertical;
  overflow:hidden;
}
```
效果图：
![效果图](https://upload-images.jianshu.io/upload_images/11827773-0c0d7d2372c6847e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 8、中文单行两端对齐

单行两端对齐的实现原理：`text-align:justify;`可以设置对齐方式为两端对齐但是只对多行文本有效果，想要实现单行文本需以下操作：
1. 首先要把内联元素变为`inline-block`（为了设置宽度）
2. 然后使用after伪元素为本来只有一行`span`的增加一行使得justify起作用（after伪元素的宽度需设置为100%用来撑起两端对齐的宽度）
3. 为`span`元素设置`overflow:hidden;` 把after伪元素增加的那一行隐藏起来使布局美观
```
span{
  text-align:justify;
  display:inline-block;
  overflow:hidden;
  line-height:20px;
  height:20px;
  width:4em;
}
span::after{
  content:'';
  display:inline-block;
  width:100%;
}
```
效果图：姓名和联系方式都是两端对齐
![效果图](https://upload-images.jianshu.io/upload_images/11827773-7697b79293cf929a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 9、在不设置高度的情况下，做一个高度为40px的div且内容垂直居中

这种设置方式，可以直接让文字垂直居中，并且带有响应式，贯彻一个理念：**不要轻易设置height**。

```
div{
border:1px solid red;
text-align:center;
line-height:24px;
padding:8px;
}
```

## 10、做一个空的宽高自适应的正方形
直接设置一个空的div样式为`{ padding-top:100% }` 即可。
可以自适应父元素的宽高并形成一个空的不超出父元素且范围的最大的正方形。
效果图（父元素设置了padding）：
![效果图](https://upload-images.jianshu.io/upload_images/11827773-256b6463bf4b346c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
