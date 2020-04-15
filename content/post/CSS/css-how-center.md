---
title: "CSS如何居中"
date: 2018-05-22
draft: false
categories: ["CSS"] 
tags: ["CSS"]
---

本文翻译自[CSS Tricks](https://css-tricks.com/centering-css-complete-guide/) 中关于居中的相关介绍。

## 1、水平居中

- ### 内联元素的水平居中（如链接或文本）
可以在一个块级别的父元素中水平地居中内联元素，只需：
```css
.center-children {
  text-align: center;
}
```
注：这种写法适用于**inline**, **inline-block**, **inline-table**, **inline-flex**等等。
代码示例：
=> html代码
```html
<header>
  This text is centered.
</header>

<nav role='navigation'>
  <a href="#0">One</a>
  <a href="#0">Two</a>
  <a href="#0">Three</a>
  <a href="#0">Four</a>
</nav>  
```
=> css代码
```css
body {
  background: #f06d06;
}

header, nav {
  text-align: center;
  background: white;
  margin: 20px 0;
  padding: 10px;
}

nav a {
  text-decoration: none;
  background: #333;
  border-radius: 5px;
  color: white;
  padding: 3px 8px;
}
```
=> 效果图
![内联元素水平居中](https://upload-images.jianshu.io/upload_images/11827773-85ceb6287253ba35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- ### 块级元素水平居中
可以通过给它设置`margin-left`和`margin-right`为`auto` （并且该元素需有`width`属性，否则它将占满整行，无法居中）来居中一个块级元素。通常用这样的速记法来做：
```css
.center-me {
  margin: 0 auto;
}
```
注：无论您所处的块级别元素的宽度是多少，还是父节点，这都是可行的。
注意：你不能浮动一个元素到水平居中的位置 ，不过有一个[小技巧](https://css-tricks.com/float-center/)（本文不再详述，可点击链接自行查看）.
代码示例：
=> html代码
 ```html
<main>
  <div class="center">
    I'm a block level element and am centered.
  </div>
</main>
```
=> css代码
```css
body {
  background: #f06d06;
}

main {
  background: white;
  margin: 20px 0;
  padding: 10px;
}

.center {
  margin: 0 auto;
  width: 200px;
  background: black;
  padding: 20px;
  color: white;
}
```
=> 效果图
![块级元素居中](https://upload-images.jianshu.io/upload_images/11827773-aa76cecbcc1e3955.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- ### 两个及以上的多个块级元素水平居中
如果您有两个或多个块级别的元素，它们需要在一行中水平居中，那么最好将它们作为不同的`display`。比如设置其为`display:inline-block`。

**代码示例1：**
注：下面的示例中，inline-block-center的`<main>`标签中，是把父元素设置`text-align:center`，子元素设置`display:inline-block`；
flex-center的`<main>`标签中，使用了CSS3中新增的[弹性盒子](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes)（附带MDN相关介绍）是把父元素设置`display: flex;justify-content: center;`
=>html代码
```html
<main class="inline-block-center">
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
</main>

<main class="flex-center">
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
</main>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  margin: 20px 0;
  padding: 10px;
}

main div {
  background: black;
  color: white;
  padding: 15px;
  max-width: 125px;
  margin: 5px;
}

.inline-block-center {
  text-align: center;
}
.inline-block-center div {
  display: inline-block;
  text-align: left;
}

.flex-center {
  display: flex;
  justify-content: center;
}
```
=>效果图
![多个块级元素水平居中](https://upload-images.jianshu.io/upload_images/11827773-8ddc99f567084e4c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**代码示例2：**
注：下面的例子中，表现的是你有多个块级元素堆叠在一起，在这种情况下，自动边距（即`margin:0 auto`）仍然很好。即多个块级元素不需要在一行显示，居中的方法仍使用`margin:0 auto`。
=>html代码
```html
<main>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row. I have more content in me than my siblings do.
  </div>
  <div>
    I'm an element that is block-like with my siblings and we're centered in a row.
  </div>
</main>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  margin: 20px 0;
  padding: 10px;
}

main div {
  background: black;
  margin: 0 auto;
  color: white;
  padding: 15px;
  margin: 5px auto;
}

main div:nth-child(1) {
  width: 200px;
}
main div:nth-child(2) {
  width: 400px;
}
main div:nth-child(3) {
  width: 125px;
}
```
=>效果图
![](https://upload-images.jianshu.io/upload_images/11827773-2f072026d46caf5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 2、垂直居中

- ### 2.1 内联元素垂直居中
**2.1.1** 单行内联元素垂直居中
**代码示例1：**
单行内联元素使用**上下相同的padding**造成垂直居中的效果
```css
.link {
  padding-top: 30px;
  padding-bottom: 30px;
}
```
=>效果图
![使用padding垂直居中](https://upload-images.jianshu.io/upload_images/11827773-2aea7cc7174c86cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**代码示例2：**
当某些情况下，不适合使用padding填充时，可以让其**文本的行高=父元素的高度**达到垂直居中的效果。
=>html代码
```html
<main>
  <div>
    I'm a centered line.
  </div>
</main>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  margin: 20px 0;
  padding: 40px;
}

main div {
  background: black;
  color: white;
  height: 100px;
  line-height: 100px;
  padding: 20px;
  width: 50%;
  white-space: nowrap;
}
```
=>效果图
![使用行高垂直居中](https://upload-images.jianshu.io/upload_images/11827773-2567b4885a707b07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.1.2** 多行内联元素垂直居中
**代码示例1：**
使用**表格布局**居中（基本过时的用法）
注：下面的例子中，第一个效果图展示的是直接使用`<table>`标签（其`<td>`标签默认垂直居中），第二个效果图展示的是父元素`<div>`设置`display:table`，子元素`<p>`设置`display: table-cell;vertical-align: middle;`
=>html代码
```html
<table>
  <tr>
    <td>
      I'm vertically centered multiple lines of text in a real table cell.
    </td>
  </tr>
</table>

<div class="center-table">
  <p>I'm vertically centered multiple lines of text in a CSS-created table layout.</p>
</div>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
}

table {
  background: white;
  width: 240px;
  border-collapse: separate;
  margin: 20px;
  height: 250px;
}

table td {
  background: black;
  color: white;
  padding: 20px;
  border: 10px solid white;
  /* default is vertical-align: middle; */
}

.center-table {
  display: table;
  height: 250px;
  background: white;
  width: 240px;
  margin: 20px;
}
.center-table p {
  display: table-cell;
  margin: 0;
  background: black;
  color: white;
  padding: 20px;
  border: 10px solid white;
  vertical-align: middle;
}
```
=>效果图
![table布局](https://upload-images.jianshu.io/upload_images/11827773-aebb9a92b498a009.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![display:table布局](https://upload-images.jianshu.io/upload_images/11827773-da3057f5a632e29c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**代码示例2：**
使用**flexbox**来垂直居中。
精简CSS代码如下：
```css
.flex-center-vertically {
  display: flex;
  justify-content: center;
  flex-direction: column;
  height: 400px;
}
```
具体示例：
=>html代码
```html
<div class="flex-center">
  <p>I'm vertically centered multiple lines of text in a flexbox container.</p>
</div>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
}

div {
  background: white;
  width: 240px;
  margin: 20px;
}

.flex-center {
  background: black;
  color: white;
  border: 10px solid white;
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 200px;
  resize: vertical;
  overflow: auto;
}
.flex-center p {
  margin: 0;
  padding: 20px;
}
```
=>效果图
![flexbox垂直居中](https://upload-images.jianshu.io/upload_images/11827773-2cba4aaba5e09826.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**代码示例3：**
使用**伪元素**来垂直居中。
精简CSS代码如下：
```css
.ghost-center {
  position: relative;
}
.ghost-center::before {
  content: " ";
  display: inline-block;
  height: 100%;
  width: 1%;
  vertical-align: middle;
}
.ghost-center p {
  display: inline-block;
  vertical-align: middle;
}
```
=>html代码
```html
<div class="ghost-center">
  <p>I'm vertically centered multiple lines of text in a container. Centered with a ghost pseudo element</p>
</div>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
}

div {
  background: white;
  width: 240px;
  height: 200px;
  margin: 20px;
  color: white;
  resize: vertical;
  overflow: auto;
  padding: 20px;
}

.ghost-center {
  position: relative;
}
.ghost-center::before {
  content: " ";
  display: inline-block;
  height: 100%;
  width: 1%;
  vertical-align: middle;
}
.ghost-center p {
  display: inline-block;
  vertical-align: middle;
  width: 190px;
  margin: 0;
  padding: 20px;
  background: black;
}
```
=>效果图
![伪元素垂直居中](https://upload-images.jianshu.io/upload_images/11827773-58074c81920c1451.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- ### 2.2 块级元素垂直居中
不知道网页布局的高度是很常见的，原因有很多：如果宽度改变，文本流会改变高度。文本样式的差异可以改变高度。文本数量的差异可以改变高度。具有固定宽宽比的元素，如图像，可以在重置宽高时改变高度等等。
但是如果你已经知道高度，就可以这样：

**2.2.1** 块级元素的高度已知/确定 
即设置子元素相对于父元素绝对定位，子元素设置`top：50%`，并设置`margin-top`为负的子元素自身高度的一半。
```css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  height: 100px;
  margin-top: -50px; /* account for padding and border if not using box-sizing: border-box; */
}
```

代码示例：
注：
=>html代码
```html
<main>
  
  <div>
     I'm a block-level element with a fixed height, centered vertically within my parent.
  </div>
  
</main>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  height: 300px;
  margin: 20px;
  width: 300px;
  position: relative;
  resize: vertical;
  overflow: auto;
}

main div {
  position: absolute;
  top: 50%;
  left: 20px;
  right: 20px;
  height: 100px;
  margin-top: -70px;
  background: black;
  color: white;
  padding: 20px;
}
```
=>效果图
![](https://upload-images.jianshu.io/upload_images/11827773-785975d12195f497.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.2.2** 块级元素的高度未知/不确定
它仍然可以通过将它的一半高度推到一半的高度来居中，即设置子元素` transform: translateY(-50%);` ，即：
```css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
}
```
=>html代码
```html
<main>
  
  <div>
     I'm a block-level element with an unknown height, centered vertically within my parent.
  </div>
  
</main>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  height: 300px;
  margin: 20px;
  width: 300px;
  position: relative;
  resize: vertical;
  overflow: auto;
}

main div {
  position: absolute;
  top: 50%;
  left: 20px;
  right: 20px;
  background: black;
  color: white;
  padding: 20px;
  transform: translateY(-50%);
  resize: vertical;
  overflow: auto;
}
```
=>效果图
![](https://upload-images.jianshu.io/upload_images/11827773-0d4f724eac319606.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**2.2.3** 使用`display:flex`布局
即：
```css
.parent {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
```
=>html代码
```html
<main>
  
  <div>
     I'm a block-level element with an unknown height, centered vertically within my parent.
  </div>
  
</main>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
}

main {
  background: white;
  height: 300px;
  width: 200px;
  padding: 20px;
  margin: 20px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  resize: vertical;
  overflow: auto;
}

main div {
  background: black;
  color: white;
  padding: 20px;
  resize: vertical;
  overflow: auto;
}
```
=>效果图
![](https://upload-images.jianshu.io/upload_images/11827773-f076f04a5a5fb4da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 3、水平居中+垂直居中

- ### 固定宽高的元素
使用负的`margin`等于宽度和高度的一半，在你完全定位50%/50%之后，它会以巨大的跨浏览器支持为中心，即：
```css
.parent {
  position: relative;
}

.child {
  width: 300px;
  height: 100px;
  padding: 20px;

  position: absolute;
  top: 50%;
  left: 50%;

  margin: -70px 0 0 -170px;
}
```
代码示例：
=>html代码
```html
<main>
  
  <div>
     I'm a block-level element a fixed height and width, centered vertically within my parent.
  </div>
  
</main>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
  padding: 20px;
}

main {
  position: relative;
  background: white;
  height: 200px;
  width: 60%;
  margin: 0 auto;
  padding: 20px;
  resize: both;
  overflow: auto;
}

main div {
  background: black;
  color: white;
  width: 200px;
  height: 100px;
  margin: -70px 0 0 -120px;
  position: absolute;
  top: 50%;
  left: 50%;
  padding: 20px;
}
```
=>效果图
![](https://upload-images.jianshu.io/upload_images/11827773-5cbb2a520434d949.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- ### 宽高未知的元素
如果你不知道宽度和高度，你可以使用transform属性和在两个方向上的负translate（它基于当前元素的宽度/高度）到中心，即：
```css
.parent {
  position: relative;
}
.child {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```
代码示例：
=>html代码
```html
<main>
  
  <div>
     I'm a block-level element of an unknown height and width, centered vertically within my parent.
  </div>
  
</main>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
  padding: 20px;
}

main {
  position: relative;
  background: white;
  height: 200px;
  width: 60%;
  margin: 0 auto;
  padding: 20px;
  resize: both;
  overflow: auto;
}

main div {
  background: black;
  color: white;
  width: 50%;
  transform: translate(-50%, -50%);
  position: absolute;
  top: 50%;
  left: 50%;
  padding: 20px;
  resize: both;
  overflow: auto;
}
```
=>效果图
![](https://upload-images.jianshu.io/upload_images/11827773-b580cc10159af85f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- ### 使用flexbox居中
要用flexbox向两个方向居中，你需要使用两个中心属性，即：
```css
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}
```
=>html代码
```html
<main>
  
  <div>
     I'm a block-level-ish element of an unknown width and height, centered vertically within my parent.
  </div>
  
</main>
```
=>css代码
```css
body {
  background: #f06d06;
  font-size: 80%;
  padding: 20px;
}

main {
  background: white;
  height: 200px;
  width: 60%;
  margin: 0 auto;
  padding: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  resize: both;
  overflow: auto;
}

main div {
  background: black;
  color: white;
  width: 50%;
  padding: 20px;
  resize: both;
  overflow: auto;
}
```
=>效果图
![](https://upload-images.jianshu.io/upload_images/11827773-624191a185674c76.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- ### 使用grid居中
这只是一个非常有效的小技巧（由Lance Janssen上传）
=>html代码
```html
<div class="wrapper">
    <span> I'm centered!</span>
</div>
```
=>css代码
```css
.wrapper{
  height:300px;
  width:300px;
  border:1px solid red;
  display: grid;
}
span {
  margin: auto;
}
```
=>效果图
![](https://upload-images.jianshu.io/upload_images/11827773-d23514099ff5e4ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)