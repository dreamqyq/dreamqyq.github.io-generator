---
title: "CSS中的flex布局详解"
date: 2018-06-12
draft: false
categories: ["CSS"] 
tags: ["CSS"]
---

**前言**：之前我写过的一篇博客介绍CSS常用的几种布局方式，PC端最常见的就是浮动布局和flex布局，而在移动端，由于不用顾忌IE这个业界大坑，flex布局应用的就更广泛了，多以本文就来详细的介绍一些flex布局。

## 1、先不说flex布局

首先我们回顾一下除了flex布局之外的常用布局方案：
首先是最基础的normal flow（文档流），子元素float+父元素添加clearfix类清除浮动，相对定位结合绝对定位，有时候还可以用负的margin来布局元素等等。而这些布局设定起来都相对比较复杂，要么得计算确切的位置，要么得防止浮动带来的bug。直到flex布局的出现，人们有了更好的布局解决方案。

## 2、flex布局的特点
- 块级布局侧重垂直方向，行内布局侧重水平方向，而flex布局就厉害了，他的**布局与方向无关**（或者说方向是可以改变的）
- flex布局可以实现**空间的自动分配**，**自动对齐**（恰好印证了flexible这个词，弹性，灵活）
- flex布局适用于**简单的线性布局**，更复杂的、多维度的布局要交给grid布局

## 3、flex中的基本概念
![](https://upload-images.jianshu.io/upload_images/11827773-86cedc62c673900b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


采用 Flex 布局的元素，称为 **Flex 容器（flex container）**，简称"容器"。它的所有子元素自动成为容器成员，称为 **Flex 项目（flex item）**，简称"项目"。
容器默认存在两根轴：水平的**主轴（main axis）**和垂直的**横轴（cross axis）**。主轴的开始位置（与边框的交叉点）叫做**main start**，结束位置叫做**main end**；横轴的开始位置叫做**cross start**，结束位置叫做**cross end**。
项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据的横轴空间叫做cross size。

## 4、flex container 的属性

- `flex-direction`：设置主轴的方向，属性有：row (水平，默认值)/ row-reverse(水平反向) / column(竖直) / column-reverse(竖直反向)
- `flex-wrap`：设置是否换行（默认为nowrap不换行），属性有：nowrap / wrap / wrap-reverse(换行，且第一行在下面)
- `flex-flow`：上面二者的简写，如：`flex-flow: row wrap;`
- `justify-content`：设置主轴的对齐方式，属性有：flex-start(默认值) / flex-end / center / space-between (两端对齐，项目之间的间隔都相等) / space-around (每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。)
- `align-items`：设置横轴的对齐方式，属性有：flex-start / flex-end / center / baseline(按项目的第一行文字的基线对齐) / stretch(默认值，如果项目未设置高度或设为auto，将占满整个容器的高度);
- `align-content`：多行/列内容对齐方式（多行中间的间隔，单行则无效），属性有：flex-start / flex-end / center / space-between / space-around / stretch

## 5、flex item 的属性

- `flex-grow`：定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大
- `flex-shrink`：定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
- `flex-basis`：定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
- `flex`：上面三个的缩写，默认值为0 1 auto
- `order`：顺序（定义项目的排列顺序。数值越小，排列越靠前，默认为0，可以为负数）
- `align-self`：允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch，属性有：auto | flex-start | flex-end | center | baseline | stretch

## 6、几个简单的例子

- **常见的手机页面布局（topbar + main + tabs）**
即全程不需要定位，浮动，仅用flex布局即可完成下图的布局结构，中间的部分超出有滚动条，header和footer位置固定。
![基本效果图](https://upload-images.jianshu.io/upload_images/11827773-7a026553c24096ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

html结构
```html
<div class="container">
		<header>header</header>
		<main>
			<ul>……</ul>
		</main>
		<footer>
			<ul>……</ul>
		</footer>		
</div>
```
css关键代码（部分省略）
```css
.container{
	display: flex;
	flex-direction: column;
	height: 100vh;
	text-align: center;
}
.container > footer > ul{
	display: flex;
	flex-direction: row;	
	justify-content: space-around;
	align-items: center;
	height: 80px;
}
.container > footer > ul > li{
	background: #666;
	width: 20%;
}
```

- **产品列表（`ul>li*9`）**
即完成父元素定宽高，内容列表依次排版的效果，不需要float，不需要特别的计算margin，仅仅依靠flex布局即可完成下图的基本布局：
![效果图](https://upload-images.jianshu.io/upload_images/11827773-7b5cf700a06a48a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
html基本结构
```html
<ul class="container">
	li{list$}*9
</ul>
```
css关键代码
```css
ul.container{
	height: 400px;
	width: 400px;
	border: 1px solid red;
	display: flex;
	flex-flow: wrap row;
	justify-content: space-between;
	align-items: center;
}
ul.container > li{
	width: 100px;
	height: 100px;
	border: 1px solid blue;
}
```

- **PC 页面布局（用flex写双飞翼）**
即header和footer定高，中间内容部分占满空间；内容部分，需要content先加载（即先写content代码，使用order控制其显示顺序），conten宽度自适应，left和right定宽，在使用flex布局的情况下，代码量远小于使用浮动。效果图如下：
![效果图](https://upload-images.jianshu.io/upload_images/11827773-b073707e5bdf8b23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
html结构
```html
<div class="container">
	<header>Header</header>
	<main>
		<div class="content">content</div>
		<aside class="asideLeft">Left</aside>
		<aside class="asideRight">Right</aside>
	</main>
	<footer>Footer</footer>	
</div>
```
css关键代码
```css
.container{
	display: flex;
	flex-direction: column;
	height: 100vh;
}
.container > header,footer{
	min-height: 80px;
	background: #aaa;
}
.container > main{
	display: flex;
	flex: 1;
}
.container > main > aside.asideLeft{
	width: 100px;
	background: #666;
	order: 1;
}
.container > main > .content{
	background: rgba(255,0,0,0.2);
	flex:1;
	order: 2;	
}
.container > main > aside.asideRight{
	width: 100px;
	background: #666;
	order: 3;
}
```


- **使用flex做成完美居中效果**
使用flex布局非常容易做到完美居中的效果，可以实现内部宽高自适应居中，父元素只需要三行代码：
```css
  display:flex;
  justify-content:center;
  align-items:center;
```

## 7、其他

两个关于学习flex基本属性的小游戏：
- [http://flexboxfroggy.com/#zh-cn](http://flexboxfroggy.com/#zh-cn "null")
- [http://www.flexboxdefense.com/](http://www.flexboxdefense.com/ "null")

两位大佬关于flex更细致的介绍：
- [张鑫旭-CSS box-flex属性，然后弹性盒子模型简介](http://www.zhangxinxu.com/wordpress/2010/12/css-box-flex%E5%B1%9E%E6%80%A7%EF%BC%8C%E7%84%B6%E5%90%8E%E5%BC%B9%E6%80%A7%E7%9B%92%E5%AD%90%E6%A8%A1%E5%9E%8B%E7%AE%80%E4%BB%8B/)
- [阮一峰-Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)


