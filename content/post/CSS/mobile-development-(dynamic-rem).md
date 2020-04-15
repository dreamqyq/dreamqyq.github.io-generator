---
title: "再识移动端开发（动态REM）"
date: 2018-07-17
draft: false
categories: ["CSS"] 
tags: ["CSS"]
---

**前言**：之前写过[一篇博客](https://www.jianshu.com/p/04d762c5e9a4)介绍响应式移动端开发，而本文会介绍一种非响应式的移动端开发技术：**动态REM**。看了这两篇文章，你将会对移动端开发有全面的了解。

## 1、前置知识

- 首先来介绍一下在写CSS中常用的长度单位：
`px`：即像素，最最常用
`rem`：即root em，表示根元素的`font-size`，根元素一般指`<html></html>`
`em`：一个“M”的宽度（但实际面试中我们回答**一个汉字的宽度**比较好）
`vh`：即viewport heigh，100vh表示当前视口高度
`vw`：即viewport width，100vw表示当前视口的宽度（兼容性太差，不建议使用）

- 网页中：一般默认字体宽度为**16px**。
在chrome浏览器中，默认最小字体大小为**12px**，即设置更小的字体大小也不会有任何效果。

- `em` 和 `rem` 的区别：
其实二者除了长得比较像，没有太大的联系
`em`指的是当前元素的`font-size`，rem指的是根元素的`font-size`。
举例说明：
```css
html{
  font-size:32px;
}
body{
  border:1px solid red;
  font-size:16px;
  height:2em; 
  /*此时高度为32px*/
  height:2rem;
  /*此时高度为64px*/
}
```

## 2、为什么要使用动态REM技术

在pc端中，网页是可以大致知道要写多宽的（比如980px），而在移动端，每一款手机的屏幕分辨率都不一样，虽然宽度的大致范围都是在300px和500px之间，但细微的差别是很明显的，只要没做好适配，就很难优雅的向用户展示网站效果。

现在在不同分辨率的手机上展示相同的网页，要求网页的布局大体上看着要差不多，最常用的有以下两种适配方案：
1. 不固定每一个元素的宽度，都用百分比来表示。但是这么写我们无法确定宽度的确切值，如果想让某个元素的高度随着宽度的缩放而缩放就实现不了啦。
2. 所以我们更希望的效果是：保持每一个元素宽高比例不变，**根据设备的宽度将元素整体放大或缩小**，而能实现这种效果的最佳方案，就是动态REM。

## 3、动态REM的使用

- 动态REM的思路：**一切单位以设备宽度为基准**，就能保证完美的还原设计稿。

- 在css中我们无法获取当前设备的宽度，所以需要在JS中需要写：
```
 var pageWidth = window.innerWidth
 document.write('<style>html{font-size:'+pageWidth+'px;}</style>')
```
即让`<html></html>`的font-size = 设备宽度，即 `1 rem = 1 pageWidth` 
然后在css中，就可以优雅的使用rem作为单位啦。
- 注意别忘了禁止移动端的自动缩放：
`<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`

- 举个例子：
```css
.child {
  width: .5rem;
  height: .25rem;
  margin: 0.0625rem 0.0625rem;
  border: 1px solid red;
  float: left;
  font-size: 1.2em;
}
```
- 然后我们会发现，这么写有点不对劲呀，每个长度的数值都是用好几位小数来表示，看着不太爽，我们或许可以让`100 rem = 1 pageWidth`？这样上面的代码，比如`margin`就可以改写成`margin: 6.25rem 6.25rem;` 看起来貌似舒服多了？？
**但是！！这样写在chrome中一定会出bug的。**还记得在第一条：前置知识中写了**在chrome浏览器中，默认最小字体大小为12px，即设置更小的字体大小也不会有任何效果。**
而移动端的宽度一般在300-500px之间，此时，1rem的值大致为3-5px，很明显已经低于最小值了，浏览器会默认将1rem = 12px，布局就彻底乱了。
所以退而求其次，我们可以在js中这么写，让：`10 rem = 1 pageWidth`
```js
 var pageWidth = window.innerWidth
 document.write('<style>html{font-size:'+pageWidth/10+'px;}</style>')
```
- 注意：比如`font-size` 、`border`之类的值已经比较小了，建议在这些属性设置时还是使用px像素就好。也就是说，rem和px是可以混用的，做移动端时，不用所有的单位都使用`rem`。

## 4、使用scss让px自动转化成rem

程序员一般都很懒，所以即使动态rem技术已经很优秀了，但是写代码的过程中，我们还是需要根据设计稿来计算每个元素和宽度的比例，然后转化为rem，岂不是效率很低，所以这一节介绍使用scss技术，让我们可以在写代码的过程中直接以px为单位，然后自动转化成rem即可。
- scss的安装与使用（使用命令行）
```bash
npm config set registry [https://registry.npm.taobao.org/](https://registry.npm.taobao.org/ "null")
touch ~/.bashrc
echo 'export SASS_BINARY_SITE="[https://npm.taobao.org/mirrors/node-sass"'](https://npm.taobao.org/mirrors/node-sass%22' "null") >> ~/.bashrc
source ~/.bashrc
```
上面的代码是设置淘宝的镜像源，让我们可以直接安装而不用科学上网。
```bash
npm i -g node-sass
mkdir ~/Desktop/scss-demo
cd ~/Desktop/scss-demo
mkdir scss css
touch scss/style.scss
start scss/style.scss
node-sass -wr scss -o css
// 上面这一行用于监听，不要中断监听，重新开一个窗口进行操作
```
上述代码即安装和使用方法

**注意**：以上命令行安装的是`node-scss`，我在安装时可能是环境没有配置好，没有成功，下面介绍`ruby-sass`的安装和使用方法：
```bash
//安装
sudo npm i -g sass
//后续操作同上，不过最后的监听命令有点区别：
sass --watch scss:css
```
我个人反而觉得`ruby-sass`的操作更简单一些，使用哪一个自己决定

编辑 scss 文件就会自动得到 css 文件
在 scss 文件里添加
```scss
@function px( $px ){
  @return $px/$designWidth*10 + rem;
}

$designWidth : 640; // 640 是设计稿的宽度，你要根据设计稿的宽度填写。如果设计师的设计稿宽度不统一，就杀死设计师，换个新的。

.child{
  width: px(320);
  height: px(160);
  margin: px(40) px(40);
  border: 1px solid red;
  float: left;
  font-size: 1.2em;
}
```




