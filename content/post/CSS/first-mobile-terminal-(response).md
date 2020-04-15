---
title: "初识移动端（响应式）"
date: 2018-06-07
draft: false
categories: ["CSS"] 
tags: ["CSS"]
---
**前言**：今天初次了解了移动端的一些常识，包括PC端和移动端的区别，媒体查询等的相关操作,以及国内的网站如何实现不同的端展示不同的样式。

---

## 1、[媒体查询](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Media_queries)（附带MDN解释）

> 一个媒体查询由一个可选的媒体类型和零个或多个使用媒体功能的限制了样式表范围的表达式组成，例如宽度、高度和颜色。媒体查询，添加自CSS3，允许内容的呈现针对一个特定范围的输出设备而进行裁剪，而不必改变内容本身。

- **例1**：（通过不同的媒体查询使不同的CSS文件生效，即css文件一定会被下载，媒体查询仅可以控制其是否生效，下面的例子是指宽度为0-800px之间时生效）
```
<!-- link元素中的CSS媒体查询 -->
<link rel="stylesheet" media="(max-width: 800px)" href="example.css" />
```
- **例2**：（通过不同的媒体查询使不同的CSS样式生效，下面的例子是指宽度在0-600px之间时样式生效）
```
<!-- 样式表中的CSS媒体查询 -->
<style>
@media (max-width: 600px) {
  .facet_sidebar {
    display: none;
  }
}
</style>
```
- **注**：通过类似例2的方式设置样式时，可能需要这样的效果：**浏览器宽度0-320px、321px-375px、376px-425px、426px以上，这四个宽度区间不同的样式**，那么该如何设置呢？
首先，如果按顺序`@media (max-width: 320px)`、 `@media (max-width: 375px)`、 ` @media (max-width:425px)`、 `@media (min-width: 426px)`这么写的化，会出现一个现象，即所有宽度的样式都是376px-426px状态下的样式，这是因为后出现的` @media (max-width:425px)`包含的是0-425px的情形，所以覆盖了前面的样式设置。
所以目前有两种写法，（1）**倒着写**，即先写宽度426px以上的，然后写376px-425px之间的，以此类推；（2）**用and连接确定区间的两个端点**，如`@media (min-width: 376px) and (max-width:425px)`

- 媒体查询需要注意的地方
1. 如果页面需要做移动端和pc端两套样式，建议`Mobile First`，即移动端优先
2. 手机端的媒体查询，一般采用`@media ( max-width:450px; )`，即最大宽度为450px左右。

## 2、关于 `meta viewport`

在移动端做网页，还需要添加这么一行代码：
`<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`

这是由历史原因的：
由于刚开始智能手机还没有出现，所有的网站都是只给PC端来看的，手机基本无法看到网页。后来诺基亚大火（此时仍然没有智能手机的出现），诺基亚用户想要比较优雅的看网页，所以很多网站为此专门做了一套“ **wap** ”页面，“ wap ”页面语与HTML法类似，但并非HTML。
直到**iPhone 3GS** 的出现，智能手机开始逐渐进入人们的视线，当时的iPhone 3GS 屏幕宽只有320px，而那时的网站一般都在九百多像素，为了在智能手机上展示网页，当时苹果的做法是：**把所有的网站都缩小**，然后可以两个手指拖动来放大页面，而这么做的缩放比例是什么呢？就是**用320px宽的屏幕模拟980px宽的网页**（980px这个值是通过市场调研所得，这是不成文的规定），所以虽然手机的分辨率宽度只有三百多像素，但是通过`document.documentElement.clientWidth`来获取网页宽度，一定得到的是980px。
而如今我们的智能手机发展迅速，基本所有网站都会专门为移动端设计样式，而手机默认会缩小我们的网站，为了阻止网页的自动缩放，所以有了这句代码：
`<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`

然后来解释一下这段代码的意思：
`name="viewport"` 记住viewport是视口的意思就好啦
`width=device-width` 宽度=设备宽度，此时获取页面宽度，设备宽就是页面宽
`user-scalable=no` 禁止用户缩放
`initial-scale=1.0` 初始缩放倍数 1.0
`maximum-scale=1.0` 最大缩放倍数 1.0
`minimum-scale=1.0` 最小缩放倍数 1.0

## 3、学会隐藏元素

移动端设计中，很重要的一条就是要学会**隐藏元素**，例如这个网站 https://www.smashingmagazine.com/，就是根据网页宽度来响应式不同的样式。以该网站的导航栏为例，它根据页面宽度大致三有种不同的样式

- 宽度较大时，它的导航栏是这样的，左边有logo，中间是导航栏的各种按钮，右边是可以点击的Topics按钮：
![完整的导航栏](https://upload-images.jianshu.io/upload_images/11827773-a9e22d727d1508ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 宽度逐渐缩小，导航栏中间按钮下边的小字消失了：
![](https://upload-images.jianshu.io/upload_images/11827773-464d5162de126493.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 宽度继续缩小，出现最后一种样式，导航栏中间的按钮消失，而右边原来的Topics按钮，现在变为Menu按钮：
![](https://upload-images.jianshu.io/upload_images/11827773-3bdae1bbb12c894c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
而Menu点击后才出现链接：
![点击Menu按钮后的样式](https://upload-images.jianshu.io/upload_images/11827773-d096315f40deaf1b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**做出上述效果，最主要的就是在不同的宽度时，让该出现的元素出现，不该出现的元素隐藏起来***

## 4、移动端与PC端设计的一些区别

- **没有 hover 效果**（因此尽量少的使用CSS中的hover可以在从PC端改到移动端开发时更少的修改代码，相反的，如果hover在开发时使用的过多，到移动端时网页样式多半就废了）
- **有 touch 事件** 移动端是可以触摸的，所以需要使用touch触摸监听
- **没有 resize** 即在移动端，浏览器的宽高是无法更改的，其宽高就等于设备的宽高。
- **没有滚动条** 横向纵向都没有滚动条，超出屏幕的内容通过滑动屏幕来观看
- **移动端需要设置滑动事件** 但是原生JS是没有滑动事件的，不过包括jquery、Vue等框架都封装的有swipe事件可以实现滑动效果

## 5、国内大多数网站的设计思路

在国内，如果网页需要在不同的设备拥有不同的样式，他们很少像第二条中展示的smashingmagazine网站一样做响应式，大体上有以下两种思路：
- 不同的端跳转到不同的域名，比如[淘宝](https://www.taobao.com/)、[京东](https://www.jd.com/)，他们的PC端会跳转到www.taobao.com域名（或www.jd.com），而移动端会跳转到h5.m.taobao.com（或m.jd.com/）
- 不同的端通过识别user Agent，来渲染不同的CSS样式，如[知乎](https://www.zhihu.com/)
- 另外，由于如今智能设备发展迅猛，如果一个页面要有多端显示样式的话，建议“mobile-first”，即优先完成移动端的样式，然后再做PC端。
