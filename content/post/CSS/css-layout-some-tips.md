---
title: "CSS左右布局、左中右布局以及一些小技巧"
date: 2018-05-22
draft: false
categories: ["CSS"] 
tags: ["CSS"]
---

## 1、左右布局

如果有以下html结构，设置左右两栏布局
```
<div class="parent">
  <div class="leftChild"></div>
  <div class="rightChild"></div>
</div>
```

- **设置浮动**：左右布局常用的方法就是为子元素设置浮动，然后在其**父元素上使用clearfix类**清除浮动。示例代码如下：
```
.clearfix::after{
  content:"";
  display:block;
  clear:both;
}
.leftChild,
.rightChild{
  float:left;
}
```
- **设置position绝对定位**，为父元素设置`position:relative;` 为子元素设置`position:absolute` 。示例代码如下：
```
.parent{
  position:relative;
}
.leftChild{
  position:absolute;
  left:0;
  top:0;
}
.rightChild{
  position:absolute;
  left:200px;
  top:0;
}
```

## 2、左中右布局

左中右布局主要方法也是浮动或者绝对定位，不过可以分情况选择其一使用甚至结合使用。


## 3、特定情况下使用浮动还是绝对定位

- 使用浮动时：不需要计算特别精确的位置，不过不易操控，浮动元素的宽度需要注意，否则会换行展示，适用于导航栏等地方。
- 使用绝对定位：需要计算元素的具体位置，不过更加准确，易于操控。
- 当某些元素的位置要根据父元素的大小自适应，内容宽度无法确定时，建议使用浮动
- 当特定元素的位置是相对父元素固定，或者内容宽高确定，需要精确定位甚至以后要用js操作变换位置时，建议使用浮动。

## 4、能解决80%以上的常用两栏、三栏布局方式
- PC端
IE——float浮动布局
Chrome等——flex弹性布局
- 移动端：flex弹性布局


## 5、其他小技巧

- 使用伪元素清除浮动 ，代码展示见下一条。
- 当要达到一个元素hover状态下有边框，正常情况下无边框时，如果直接在hover状态添加边框，该元素会因为多出来的border宽度导致位置有略微改变。技巧：可以在普通情况下就添加透明色的边框，hover时改变颜色即可。比如
html代码：
```
<nav class="clearfix">
  <li><a href="#">link1</a></li>
  <li><a href="#">link2</a></li>
  <li><a href="#">link3</a></li>
  <li><a href="#">link4</a></li>
  <li><a href="#">link5</a></li>
  <li><a href="#">link6</a></li>
</nav>
```
css代码
```
.clearfix::after{
  content: "";
  display: block;
  clear: both;
}
nav{
  border: 1px solid red;
  float: right;
}
nav > li{
  float: left;
  list-style: none;
}
nav > li > a{
  text-decoration: none;
  color:inherit;
  display: inline-block;
  margin:5px;
  padding:10px;
  border: 3px solid transparent;
  transition:0.3s;
  
}
nav > li > a:hover{
  border:3px solid blue;
}
```
效果图：
![](https://upload-images.jianshu.io/upload_images/11827773-53efec9a52663679.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 水平线样式设置
代码示例：
```
hr{
	height:0;
	border:none;
	border-top:1px solid red;
}
```
- dl中dd、dt设置左右结构
html代码
```
<body>
<main>
  <dl class="clearfix">
    <dt>num</dt>
    <dd>1</dd>
    <dt>num</dt>
    <dd>2</dd>
    <dt>num</dt>
    <dd>3</dd>
    <dt>num</dt>
    <dd>4</dd>
  </dl>
</main>
```
css代码
```
main > dl{
  width:300px;
  border: 1px solid;
}
main > dl >dt,
main > dl >dd{
  float: left;
  width:30%;
  box-sizing:border-box;
  border:1px solid blue;
}
main > dl >dd{
  width:70%;
}
```
效果图：
![](https://upload-images.jianshu.io/upload_images/11827773-37447044abd6081c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 一些图标素材可以使用[iconfont网站](http://www.iconfont.cn/) 来查找
- 合理使用伪元素（如`::after`、`::before`）
- 块级元素宽度自使用，合理使用`max-width`属性
- a标签去掉其默认样式时，颜色可设置为继承父元素`a{color:inherit;}`

