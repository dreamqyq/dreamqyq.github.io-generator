---
title: "JSONP、JSON、AJAX简述"
date: 2018-07-25
draft: false
categories: ["HTTP"] 
tags: ["HTTP"]
---

**前言**：一个网站如果要完整的运行出来，必须要结合前端与后端，而我们作为一名前端，也是需要知道一些最基本的后端接口的知识。本文就简略的介绍一下这几个技术：`JSONP`、`JSON`、`AJAX`。

## 1、如何发请求

- 用 `form` 可以发请求，但是会刷新页面或新开页面
- 用 `a` 可以发 `get` 请求，但是也会刷新页面或新开页面
- 用 `img` 可以发 `get` 请求，但是只能以图片的形式展示
- 用 `link` 可以发 `get` 请求（必须放在`<head>`中），但是只能以 CSS、favicon 的形式展示
- 用 `script` 可以发 `get` 请求（需要放在`<head>`或`<body>`中），但是只能以脚本的形式运行

以上方法除了`form` 都无法实现`get`、`post`、`put`、`delete` 等请求都行，想以什么形式展示就以什么形式展示。

## 2、什么是 JSONP？

请求方：a网站 的前端程序员（浏览器）
响应方：b网站 的后端程序员（服务器）

- 请求方动态创建 `script`，`src` 指向响应方，同时传一个查询参数 `?callbackName=yyy`
- 响应方根据查询参数`callbackName`，构造形如
  - `yyy.call(undefined, '你要的数据')`
  - `yyy('你要的数据')`
这样的响应
- 浏览器接收到响应，就会执行 `yyy.call(undefined, '你要的数据')`
- 那么请求方就知道了他要的数据
**这就是 JSONP**

注：两个约定：
`callbackName` 一般都叫 `callback`
`yyy`函数 一般使用 **随机数**来命名 如：`enoch12312312312321325()`

## 3、JSONP 为什么不支持 POST

答：因为JSONP是动态创建`script`来实现的，而动态创建`script`不支持post请求，只能用get请求。

## 4、jQuery 的 JSONP方法
```js
 $.ajax({
 url: "http://jack.com:8002/pay",
 dataType: "jsonp",
 success: function( response ) {
     if(response === 'success'){
     amount.innerText = amount.innerText - 1
     }
   }
 })
```
**需要注意的是：虽然jQuery中，把JSONP归到ajax方法中，但实际上JSONP和ajax没有一丝关系，是核心思路完全不同的两种方法。**

## 5、什么是AJAX

Jesse James Garrett 将如下技术取名叫做 AJAX：**异步的 JavaScript 和 XML**，即 **Async JavaScript And XML**
- 使用 XMLHttpRequest 发请求
- 服务器返回 XML 格式的**字符串**
- JS 解析 XML，并更新局部页面

注：现在已经很少使用XML，更多的使用JSON，即服务器返回**符合JSON语法的字符串**。

## 6、如何使用 XMLHttpRequest

- `readyState`属性：请求的5种状态，值为0~4，**至少一定要知道值为4的含义**。
  - 0 ：UNSENT (未打开)	open()方法还未被调用.
  - 1 ：OPENED  (未发送)	 open()方法已经被调用.
  - 2 ：HEADERS_RECEIVED (已获取响应头)	send()方法已经被调用, 响应头和响应状态已经返回.
  - 3 ：LOADING (正在下载响应体)	响应体下载中; responseText中已经获取了部分数据.
  - **4 ：DONE (请求完成)	整个请求过程已经完毕.**

- 核心代码如下：
前端代码：
```js
btn.addEventListener('click',(e) => {
  let request = new XMLHttpRequest()  //产生request对象
  request.open('post','/xxx')  // 配置request对象
  request.send()  // 发送这个对象 
  request.onreadystatechange = ()=>{ // 监听request对象的readyState的变化
    if(request.readyState === 4){
      console.log('请求完成')
      if(request.status >= 200 && request.status < 400){
        console.log('请求成功')
        let string = request.responseText
        console.log(string)
        let obj = JSON.parse(string)
        console.log(obj.note.body)
        
      }else{
        console.log('请求失败')
      }
    }
  }
})
```
后端代码：
```js
if(path ==='/xxx'){
    response.setHeader('Content-Type','text/json;charset=utf-8')
    response.write(`
      {
        "note":{
          "to":"Enoch",
          "from":"Snow",
          "heading":"Say Hi",
          "body":"Hello Wrold"
        }
      }
    `)
    response.end()
}
```

## 7、JSON —— 一门新语言

> **JSON**（**J**ava**S**cript **O**bject **N**otation）是一种由道格拉斯·克罗克福特构想和设计、轻量级的**数据交换语言**。

- [JSON的全部语法](https://www.json.org/)
JSON的数据类型：
![](https://upload-images.jianshu.io/upload_images/11827773-f88be6b92bcf66c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- JSON 与 JS 的区别：
  - JSON 和 JS 是两门不同的语言，JSON借鉴（或者说抄袭）了JavaScript的部分语法。
  - JSON没有`undefined`和`function`
  - JSON 字符串的首尾必须使用双引号`"`
  - ![](https://upload-images.jianshu.io/upload_images/11827773-c0f637e2292766ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 举个栗子：
```json
"note":{
  "to":"Enoch",
  "from":"Snow",
  "heading":"Say Hi",
   "body":"Hello Wrold"
 }

```

## 8、同源策略

[阮一峰博客-浏览器同源政策及其规避方法](http://www.ruanyifeng.com/blog/2016/04/same-origin-policy.html)

简单的说就是：只有 **协议+端口+域名**这三项 **一模一样**才允许发 AJAX 请求。
举个栗子：
1.  [http://baidu.com](http://baidu.com/ "null") 可以向 [http://www.baidu.com](http://www.baidu.com/ "null") 发 AJAX 请求吗 **no**
2.  [http://baidu.com:80](http://baidu.com/ "null") 可以向 [http://baidu.com:81](http://baidu.com:81/ "null") 发 AJAX 请求吗 **no**


## 9、CORS 跨域
[阮一峰博客-跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
> CORS是一个W3C标准，全称是"跨域资源共享"（Cross-origin resource sharing）。

浏览器必须保证只有 协议+端口+域名 一模一样才允许发 AJAX 请求
CORS 策略 可以告诉浏览器，我俩一家的，别阻止他
突破同源策略 === 跨域

举个栗子：这么写就可以让`http://enoch.com:8001`这个网站向我的网站发请求
`    response.setHeader('Access-Control-Allow-Origin','http://enoch.com:8001')
`




