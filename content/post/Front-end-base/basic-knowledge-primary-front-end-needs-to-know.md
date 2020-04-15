---
title: "初级前端需要知道的基础知识"
date: 2018-07-18
draft: false
categories: ["前端基础"] 
---

**前言**：本文会罗列出十道比较基础的题目，我个人作为刚入门的初级前端，写这篇文章正好可以总结汇总一下近期的知识，看到这篇文章的朋友也可以看看这十道题你是否都能正确的、完整的答出来。我个人会总结出答案放于文后，欢迎讨论。

---
## 题目详情：
### 1、请写出一个符合 W3C 规范的 HTML 文件，要求如下：
1. 页面标题为「我的页面」
2. 页面中引入了一个外部 CSS 文件，文件路径为 /style.css
3. 页面中引入了另一个外部 CSS 文件，路径为 /print.css，该文件仅在打印时生效
4. 页面中引入了另一个外部 CSS 文件，路径为 /mobile.css，该文件仅在设备宽度小于 500 像素时生效
5. 页面中引入了一个外部 JS 文件，路径为 /main.js
6. 页面中引入了一个外部 JS 文件，路径为 /gbk.js，文件编码为 GBK
7. 页面中有一个 SVG 标签，SVG 里面有一个直径为 100 像素的圆圈，颜色随意
8. 注意题目中的路径

### 2、 移动端是怎么做适配的？
注：回答要点：
1.  meta viewport
2.  媒体查询
3.  动态 rem 方案

### 3、用过CSS3吗? 实现圆角矩形和阴影怎么做?
### 4、什么是闭包，闭包的用途是什么？
### 5、函数中`call`、`apply`、`bind` 的用法分别是什么？
### 6、请说出至少 8 个 HTTP 状态码，并描述各状态码的意义。
### 7、请写出一个 HTTP post 请求的内容，包括四部分。
其中
第四部分的内容是 `username=ff&password=123`
第二部分必须含有 `Content-Type` 字段
请求的路径为 `/path`
### 8、请说出至少三种排序的思路，这三种排序的时间复杂度分别为
1. O(n*n)
2. O(n log2 n)
3. O(n + max)
### 9、一个页面从输入 URL 到页面加载显示完成，这个过程中都发生了什么？
### 10、如何实现数组去重？
假设有数组 `array = [1,5,2,3,4,2,3,1,3,4]`
你要写一个函数 unique，使得
`unique(array)` 的值为 `[1,5,2,3,4]`
也就是把重复的值都去掉，只保留不重复的值。

要求：
1. 不要做多重循环，只能遍历一次
2. 请给出两种方案，一种能在 ES 5 环境中运行，一种能在 ES 6 环境中运行（提示 ES 6 环境多了一个 Set 对象）


---
## 参考答案
注：答案为我个人总结而得，仅供参考，欢迎讨论。

---

### No.1 HTML基本格式 & 媒体查询 & `<svg>` & 文件编码
```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>我的页面</title>
	<link rel="stylesheet" href="./style.css">
	<link rel="stylesheet" href="./print.css" media="print">
	<link rel="stylesheet" href="./mobile.css" media="(max-width:500px)">
</head>
<body>
	<svg>
		<circle cx="50" cy="50" r="50" fill="red"/>
	</svg>
	<script src="./main.js"></script>
	<script src="./gbk.js" charset="gbk"></script>
</body>
</html>
```
- 媒体查询详见下一题。
- 关于文件编码，一般情况下都是后端来设置，但是为了以防万一，我们可以在引入如css，js文件时，顺便也设置一下引入文件的文件编码保证不会出错。
举例说明：`<script src="./gbk.js" charset="gbk"></script>`
- 关于`<svg>`，详情可以看[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/SVG)，这里仅对题目要求进行解释：
`<svg>`中，画圆使用`<circle>`标签，其中`cx`、`cy`属性指的是圆心的坐标，`r`指的是圆的半径，需要注意的是，`svg`填充颜色用的是`fill`属性。

---

### No.2 移动端适配：`meta viewport` & 媒体查询 & 动态rem
关于`meta viewport` 和 媒体查询详情可参考[这篇博客](https://www.jianshu.com/p/04d762c5e9a4)
关于动态rem详情可参考[这篇博客](https://www.jianshu.com/p/f75ac4e8102a)

移动端的适配有以下要点：
- 首先必须使用这么一行代码来禁止手机自动缩放页面：
`<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">` ， 
其中`width=device-width`意思是页面宽度等于设备宽度、 `user-scalable=no` 禁止用户缩放页面、`initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`即原始缩放比例，最大缩放比例，最小缩放比例都是1.0

- 使用 media 查询来响应不同分辨率
如在引入外部样式时，只有满足条件最大宽度为800px时，example.css才生效：
`<link rel="stylesheet" media="(max-width: 800px)" href="example.css" />`
在CSS内部，也可以用media来设置特定的情况下让某些样式生效，如下，即当最大宽度为600px时，该样式生效：
```css
<style>
  @media (max-width: 600px) {
     ……
  }
</style>
```


- 使用动态 REM 方案保证手机端的显示效果
rem指的是根元素的字体大小，根元素一般指的是`<html>`
动态rem即在js中设置
```js
var pageWidth = window.innerWidth
 document.write('<style>html{font-size:'+pageWidth/10+'px;}</style>')
```
让1rem=0.1设备宽度，然后所有的css宽高使用rem为单位即可。
使用scss可以自动让px转化为rem：
```scss
@function px( $px ){
  @return $px/$designWidth*10 + rem;
}
$designWidth : 640; // 640 是设计稿的宽度，你要根据设计稿的宽度填写。
```

---

### No.3 CSS3 ＆ 圆角矩形 ＆ 阴影

- [CSS3 MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS3)
css3 中的新特性，比较常用的：
新增加的伪类如`nth-child()`等、`flex`布局、使用`transform`对元素进行变换、`transition`过度，通过定义`keyframes`做一些动画效果、添加阴影`box-shadow`、移动端适配中会用到媒体查询等。
- [border-radious 文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border-radius)
  - 后面接一个值时：为元素的四个角都设置相同的圆角；
  - 后面接两个值：第一个值设置的是左上角和右下角的值，第二个值指的是右上角和左下角的值；
  - 后面接四个值时：四个值分别设置的圆角顺序为，从左上角开始顺时针的四个角，即分别为左上角，右上角，右下角，左下角。
  - 如果我们要做一个圆形，可以这么写：`border-radious:50%;`
  - 使用圆角还可以做椭圆：border-radius还可以用斜杠设置第二组值。这时，第一组值表示水平半径，第二组值表示垂直半径。第二组值也可以同时设置1到4个值，应用规则与第一组值相同。如：`border-radius: 15px 5px 25px 5px / 3px 5px 10px 15px;`
- [box-shadow 文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/box-shadow)
`box-shadow` 可以使用一个或多个投影，如果使用多个投影时必须需要用逗号“，”分开。用法如下：<br>
`对象选择器 {box-shadow:inset x-offset y-offset blur-radius spread-radius color}` <br>
`对象选择器 {box-shadow:投影方式 X轴偏移量 Y轴偏移量 阴影模糊半径 阴影扩展半径 阴影颜色}` <br>

---

### No.4 闭包

- 闭包的定义：如果一个函数使用了它范围外的变量，那么（这个函数和这个变量）就叫做闭包。举个例子：
```javascript
function foo(){
  var local = 1
  function bar(){
    local++
    return local
  }
  return bar
}

var func = foo()
func()
```
上述代码中，变量`local`和函数`bar`合称为闭包，即在函数`bar `中使用了函数外定义的变量`local`。

- 闭包的用途：我们之所以使用闭包，是应为闭包可以用来「间接访问一个变量」。换句话说，「隐藏一个变量」。
举个例子：
```js
!function(){

  var lives = 50

  window.addLives = function(){
    lives += 1
  }

  window.reduceLives = function(){
    lives -= 1
  }

}()
```
上述代码中，变量`lives`为局部变量，该立即函数的外面是无法直接访问该变量的，在该立即执行函数内部，声明了`addLives`函数和`reduceLives`函数，由此我们可以在函数外部通过调用这两个函数来间接地调用`lives `这个变量。这就是闭包的意义所在。

总结：闭包的用处：
1. 可以间接调用函数内部的局部变量。
2. 可以让这些变量的值始终保持在内存中。
3. 可以暂存数据,给变量开辟私密空间,避免外部污染

---

### No.5 `call` & `apply` & `bind` 的用法
参考[博客](http://www.cnblogs.com/coco1s/p/4833199.html)

在 javascript 中，call 和 apply 都是为了改变某个函数运行时的上下文（context）而存在的，换句话说，就是为了改变函数体内部 this 的指向。
- `call()` 方法调用一个函数, 其具有一个指定的this值和分别地提供的参数(参数的列表)。

- `apply()` 方法调用一个函数, 其具有一个指定的this值，以及作为一个数组（或类似数组的对象）提供的参数。

- `bind()` 方法创建一个新的函数，被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。

- 对于` apply`、`call `二者而言，作用完全一样，只是接受参数的方式不太一样，`call `需要**把参数按顺序传递进去**，而 `apply `则是**把参数放在数组里**。
- `apply` 、` call `、`bind `三者都是用来改变函数的this对象的指向的；
`apply `、 `call `、`bind` 三者第一个参数都是this要指向的对象，也就是想指定的上下文；
`apply `、 `call` 、`bind `三者都可以利用后续参数传参；
`bind` 是返回对应函数，便于稍后调用；`apply` 、`call `则是立即调用 。

举例说明：
```js
var obj = {
    x: 81,
};
 
var foo = {
    getX: function() {
        return this.x;
    }
}
 
console.log(foo.getX.bind(obj)());  //81
console.log(foo.getX.call(obj));    //81
console.log(foo.getX.apply(obj));   //81

// 三个输出的都是81，但是注意看使用 bind() 方法的，他后面多了对括号。
//也就是说，区别是，当你希望改变上下文环境之后并非立即执行，而是回调执行的时候，使用 bind() 方法。而 apply/call 则会立即执行函数。
```

---

### No.6 HTTP 状态码
[HTTP状态码 维基百科](https://zh.wikipedia.org/wiki/HTTP%E7%8A%B6%E6%80%81%E7%A0%81)
[一篇更形象的介绍状态码的博客](https://blog.csdn.net/q1056843325/article/details/53147180)

下面列举一些比较常见的状态码
- 1xx消息：代表请求已被接受，需要继续处理。这类响应是临时响应，只包含状态行和某些可选的响应头信息，并以空行结束。（不常用，大致知道即可）

2XX：表示成功
- 200 OK：请求已成功，请求所希望的响应头或数据体将随此响应返回。
- 204 No Content：表示请求已成功处理，但是没有内容返回，意思等同于请求执行成功，但是没有数据，浏览器不用刷新页面，也不用导向新的页面。
- 206 Partial Content：服务器已经成功处理了部分GET请求。（客户端进行了范围请求） 响应报文中包含Content-Range指定范围的实体内容。

3XX：重定向
- 301 Moved Permanently：表示访问的资源被永久移除，即请求的资源已经永久的搬到了其他位置 。
- 302 Found ：表示所访问的资源临时被移除，以后还会回来，即临时重定向，表示请求的资源临时搬到了其他位置 。
- 303 See Other ：表示请求资源存在另一个URI，应使用GET定向获取请求资源 。
- 304 Not Modified ：表示资源未被修改，即客户端仍然具有以前下载的副本，不需要重新传输资源。
- 307 Temporary Redirect：临时重定向，和302有着相同含义 ，尽管302标准禁止POST变为GET，但没人听他的 ，而307就会遵照标准，不会从POST变为GET 
但处理响应行为，各个浏览器可能不同。

4XX：客户端错误
- 400 Bad Request：表示请求报文存在语法错误或参数错误，服务器不理解 ，服务器不应该重复提交这个请求 ，需要修改请求内容后再次发送。
- 401 Unauthorized：表示发送的请求需要有HTTP认证信息或者是认证失败了 ，返回401的响应必须包含一个适用于被请求资源的WWW-Authenticate首部以质询用户信息 ，浏览器初次接受401时，会弹出认证窗口。
- 403 Forbidden ：表示对请求资源的访问被服务器拒绝了 ，服务器可以对此作出解释，也可以不解释 ，想说明的话可以在响应实体的主体部分描述原因 。比如说你可能没有访问权限。
- 404 Not Found：表示服务器找不到你请求的资源 ，但允许用户的后续请求。

5XX：服务器错误
- 500 Internal Server Error：通用错误消息，服务器遇到了一个未曾预料的状况，导致了它无法完成对请求的处理。没有给出具体错误信息。
- 502 Bad Gateway：作为网关或者代理工作的服务器尝试执行请求时，从上游服务器接收到无效的响应。
- 503 Service Unavailable：表示服务器超负载或正停机维护，无法处理请求 ，如果服务器知道还需要多长时间，就写入Retry-After首部字段返回。

---

### No.7 HTTP post 请求
具体格式，参照[博客](https://www.jianshu.com/p/109de7b74813)
参考答案：
```
POST /path HTTP/1.1
Host: www.baidu.com
User-Agent: curl/7.54.0
Accept: */*
Content-Length: 10
Content-Type: application/x-www-form-urlencoded

username=ff&password=123
```
注意：第二部分一般都要有`Host`、`Content-Length`、`Content-Type`

---

### No.8 排序算法
[排序算法维基百科](https://zh.wikipedia.org/wiki/%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95) ，很多前端对算法的了解都很浅，但在面试中大多时候都会考察一些算法的知识，所以最基本的排序算法我们还是应该大致了解一下的。下面大概提几个常见的排序算法思路。注：[这个网站](https://visualgo.net/bn/sorting)提供了常见排序算法的动态展示效果。
- 冒泡排序，时间复杂度O(n^2)：
遍历整个数组，比较每相邻两个元素，将小的排在前面，大的排后面，这样就可以得到最大的元素，遍历除了已经排序过的在最后的最大值，重复以上操作。
- 选择排序，时间复杂度O(n^2)：
遍历整个数组，选择一个最小的数字放在第一位，然后遍历剩下的数字，在剩下的数字中选择最小的放在第二位，以此类推。
- 插入排序，时间复杂度O(n^2)：
先排好前两个数字的顺序，从第三个数字开始，与已经排好的数字对比，将其插入到对应的位置，以此类推
- 归并排序，时间复杂度O(nlog n)：
把数据分为两段，从两段中逐个选最小的元素移入新数据段的末尾。
- 快速排序，时间复杂度O(nlog n)：
找其中的一个数作为标杆,小于标杆的数字放在左边,大于标杆的数字放在右边,这一次排完之后再重复前面的操作,直到只有一个数字为止
- 计数排序，时间复杂度O(n + max)：
统计小于等于该元素值的元素的个数i，于是该元素就放在目标数组的索引i位
- 桶排序，时间复杂度O(n)：
将值为i的元素放入i号桶，最后依次把桶里的元素倒出来。
- 基数排序，时间复杂度O(K × n)：
创建10个桶，给每个数位（0到9），从个位数开始遍历数列中的每个元素的数位，把每个元素按照队列排列到对应数位的桶中，再从队列中恢复至数列，再遍历十位数，重复以上操作，直到遍历到最大元素的位数。
- 堆排序，时间复杂度O(nlogn)：
1、将已知数组看成一个完全二叉树；
2、从二叉树最后一层最右边开始遍历（即数组的最后一个数）；
3、进行最大堆调整，使得最顶层的数成为数组中最大的数；
4、将最顶层的数与最后一层最右边的数调换位置，并使最大数不再参与下一次最大堆调整；
5、因最顶层的元素改变，从第二层开始进行最大堆调整，第二次最大堆调整使得数组中第二大的数字变换到最顶层；
6、最顶层的数与最后一层最右边的数换位并不再参与下一次最大堆调整；
7、重复5、6的步骤直至确定数组中所有数的位置；
8、输出重新排序后的数组


---

### No.9 一个页面从输入 URL 到加载显示完成，这个过程中都发生了什么

这是一道很经典的前端面试题，网上有各种各样的资料，总体来说这一题是考察你的知识边界，这里只大概的说一下相关的过程（不够全面）。
[参考博客](http://fex.baidu.com/blog/2014/05/what-happen/)：这篇博客写的非常全面，从敲击键盘开始讲起，包含了很多硬件知识以及操作系统、内存等，我本人目前知识有限，无法介绍的那么详细，有兴趣的可以点击上面的链接查看。

总体来说分为以下几个过程:
1. DNS解析：
DNS解析的过程就是寻找哪台机器上有你需要资源的过程。当你在浏览器中输入一个地址时，例如www.baidu.com，其实不是百度网站真正意义上的地址。互联网上每一台计算机的唯一标识是它的IP地址，但是IP地址并不方便记忆。用户更喜欢用方便记忆的网址去寻找互联网上的其它计算机，也就是上面提到的百度的网址。所以互联网设计者需要在用户的方便性与可用性方面做一个权衡，这个权衡就是一个网址到IP地址的转换，这个过程就是DNS解析。它实际上充当了一个翻译的角色，实现了网址到IP地址的转换。
2. TCP连接：
知道了服务器的 IP 地址，下面便开始与服务器建立连接了。
通俗地讲，通信连接的建立需要经历以下三个过程：
（1）主机向服务器发送一个建立连接的请求（您好，我想认识您）；
（2）服务器接到请求后发送同意连接的信号（好的，很高兴认识您）；
（3）主机接到同意连接的信号后，再次向服务器发送了确认信号（我也很高兴认识您），自此，主机与服务器两者建立了连接。
3. 发送HTTP请求
发送HTTP请求的过程就是构建HTTP请求报文并通过TCP协议中发送到服务器指定端口(HTTP协议80/8080, HTTPS协议443)。HTTP请求报文是由三部分组成: 请求行, 请求报头和请求正文。
数据经过应用层、传输层、网络层、数据链路层、物理层逐层封装，传输到下一个目的地。其中每一层的作用如下：
（5）应用层：为应用进程提供服务，加应用层首部封装为协议数据单元。
（4）传输层：实现端到端通信，加TCP首部封装为数据包，TCP控制了数据包的发送序列的产生，不断的调整发送序列，实现流控和数据完整。
（3）网络层：转发分组并选择路由；加IP首部封装为IP分组。
（2）数据链路层：相邻的节点间的数据传输；加首部[mac地址]和尾部封装为帧。
（1）物理层：具体物理媒介中的数据传送，数据转换为比特流。
下一个目的地接受到数据后，从物理层得到数据然后经过逐层的解包 到 链路层 到 网络层，然后开始上述的处理，在经网络层 链路层 物理层将数据封装好继续传往下一个地址。
到达最终目的地，再经过5层结构，逐层剥离，最终将数据送到目的主机的目的端口。
4. 服务器处理请求并返回HTTP报文：
后端从在固定的端口接收到TCP报文开始，这一部分对应于编程语言中的socket。它会对TCP连接进行处理，对HTTP协议进行解析，并按照报文格式进一步封装成HTTP Request对象，供上层使用。这一部分工作一般是由Web服务器去进行。
HTTP响应报文也是由三部分组成: 状态码, 响应报头和响应报文。
5. 浏览器解析渲染页面：
浏览器是一个边解析边渲染的过程。首先浏览器解析HTML文件构建DOM树，然后解析CSS文件构建渲染树，等到渲染树构建完成后，浏览器开始布局渲染树并将其绘制到屏幕上。这个过程比较复杂，涉及到两个概念: reflow(回流)和repain(重绘)。DOM节点中的各个元素都是以盒模型的形式存在，这些都需要浏览器去计算其位置和大小等，这个过程称为relow;当盒模型的位置,大小以及其他属性，如颜色,字体,等确定下来之后，浏览器便开始绘制内容，这个过程称为repain。页面在首次加载时必然会经历reflow和repain。reflow和repain过程是非常消耗性能的，尤其是在移动设备上，它会破坏用户体验，有时会造成页面卡顿。所以我们应该尽可能少的减少reflow和repain。
6. 连接结束：
（1）主机向服务器发送一个断开连接的请求（不早了，我该走了）；
（2）服务器接到请求后发送确认收到请求的信号（知道了）；
（3）服务器向主机发送断开通知（我也该走了）；
（4）主机接到断开通知后断开连接并反馈一个确认信号（嗯，好的），服务器收到确认信号后断开连接；

---

### No.10 数组去重

ES5写法1：
```js
function unique(array){
  let tempArray = []
  let hash = {}
  for(let i = 0;i<array.length;i++){
     if(hash[array[i]] === undefined){
       tempArray.push(array[i])
       hash[array[i]] = true
     }
  }
  return tempArray
}
```
ES5写法2：当数组中出现数字`3` 和字符串 ` '3' `时，第一种写法会将其当成同一个值返回，而第二种写法可以判断数字`3` 和字符串 ` '3' `的不同。
```js
function uniqueCom(array) {
  let hash = {}
  let tempArray = []
  array.forEach(function(value) {
    let key = (typeof value) + value;
    if (!hash[key]) {
      hash[key] = true
      tempArray.push(value)
    }
  })
  return tempArray
}
```
ES6写法：使用`Set`对象，[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)
注：用`...`(展开操作符)操作符将Set转换为Array
```js
function unique6(array){
  return [...new Set(array)]
}
```


