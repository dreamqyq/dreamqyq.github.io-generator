---
title: "用原生JS封装类jQuery的ajax方法"
date: 2018-07-26
draft: false
categories: ["HTTP"] 
tags: ["HTTP"]
---

**前言**：为了深入理解jQuery中的ajax方法，本文通过使用原生JavaScript来封装一个类似的方法，能实现最基本的功能。其中会使用带promise方法。

## 1、ajax 的 基本功能
首先：
`request = new XMLHttpRequest()  `

- JS 可以设置任意请求 header ：
  - 第一部分 `request.open('get', '/xxx')`
  - 第二部分 `request.setRequestHeader('content-type','x-www-form-urlencoded')`
  - 第四部分 `request.send('a=1&b=2')`
- JS 可以获取任意响应 header 吗
  - 第一部分 `response.status` / `request.statusText`
  - 第二部分 `response.getResponseHeader()` / `request.getAllResponseHeaders()`
  - 第四部分 `response.responseText`


## 2、初始版本
封装：
```js
window.jQuery.ajax = function(options){
  let method = options.method
  let url = options.url
  let headers = options.headers
  let body = options.body
  let failFn = options.failFn
  let successFn = options.successFn
  let request = new XMLHttpRequest()
  request.open(method,url)
  for(let key in headers){
    request.setRequestHeader(key,headers[key]) 
  }
  request.onreadystatechange = function(){
    if(request.readyState === 4){
      if(request.status >=200 && request.status < 400){
        successFn.call(undefined,request.responseText)
      }else{
        failFn.call(undefined,request.status)
      }
    } 
  }
  request.send(body)
}
```
调用：
```js
  jQuery.ajax({
    url:'/xxx',
    method: 'post',
    headers:{'Content-Type':'application/x-www-form-urlencoded',enoch:18},
    body:'a=1&b=2',
    successFn:(e)=>{console.log(e)},
    failFn:(e)=>{console.log(e)}
  })
```

## 3、使用ES6新语法优化

- 上面的代码中，好几行的`let`赋值看起来很傻，所以可以使用ES6的新语法：**解构赋值**来进行优化。
- 首先简单的介绍一下ES6新语法之**解构赋值**，[MDN文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 。
解构赋值语法是一个 Javascript 表达式，这使得可以将**值从数组**或**属性从对象**提取到不同的变量中。举例说明：
```js
// ES6 解构赋值示例
let a, b, rest;
[a, b] = [10, 20];
console.log(a); // 10
console.log(b); // 20

[a, b, ...rest] = [10, 20, 30, 40, 50];
console.log(a); // 10
console.log(b); // 20
console.log(rest); // [30, 40, 50]

({ a, b } = { a: 10, b: 20 });
console.log(a); // 10
console.log(b); // 20

// Stage 3 proposal
({a, b, ...rest} = {a: 10, b: 20, c: 30, d: 40});
console.log(a); // 10
console.log(b); // 20
console.log(rest); //{c: 30, d: 40}

// 对象中的key值使用变量
var x = 'a' // 让obj中的x为'a'
var obj = {
   [x]:2
}
console.log(obj) // {a: 2}
```

- 更改后的代码：
```js
window.jQuery.ajax = function({method,url,headers,body,failFn,successFn}){
  let request = new XMLHttpRequest()
  request.open(method,url)
  for(let key in headers){
    request.setRequestHeader(key,headers[key]) 
  }
  request.onreadystatechange = function(){
    if(request.readyState === 4){
      if(request.status >=200 && request.status < 400){
        successFn.call(undefined,request.responseText)
      }else{
        failFn.call(undefined,request.status)
      }
    } 
  }
  request.send(body)
}
```

## 4、使用promise优化

- 这里使用`promise`优化，主要解决的问题是：因为每个程序员的回调名不一样，你不看文档根本不知道这个库的函数名是什么，所以我们可以使用该方法不设置`successFn`、`failFn`这两个函数的函数名。

- 优化后代码如下：
封装：
```js
window.jQuery.ajax = function({method,url,headers,body}){
  return new Promise(function(resolve,reject){
    let request = new XMLHttpRequest()
    request.open(method,url)
    for(let key in headers){
      request.setRequestHeader(key,headers[key])
    }
    request.onreadystatechange = function(){
      if(request.readyState === 4){
        if(request.status >=200 && request.status < 400){
          resolve.call(undefined,request.responseText)
        }else{
          reject.call(undefined,request.status)
        }
      }
    }
    request.send(body)
  })
}
```
调用：
```js
  jQuery.ajax({
    url:'/xxx',
    method: 'post',
    headers:{'Content-Type':'application/x-www-form-urlencoded',enoch:18},
    body:'a=1&b=2',
  }).then(
    (e)=>{console.log(e)},
    (e)=>{console.log(e)}
  )
```

## 5、真正的jQuery的ajax方法

[官方文档](https://api.jquery.com/jQuery.ajax/)

`jQuery.ajax()`方法中，和我上面自己实现的有些许的名称差异：
- successFn  ==> `success`
- failFn   ==>  `error`
- headers中设置响应头的Content-Type属性   ==>   `dataType`
- data   ==>  `body`
- jQuery也支持promise写法（成功第一个参数，失败第二个参数）
- 等等

举例说明：
- jQuery普通写法：
```js
  jQuery.ajax({
    url:'/xxx',
    method: 'post',
    dataType:'x-www-form-urlencoded',
    data:'a=1&b=2',
    success:(e)=>{console.log(e)},
    error:(e)=>{console.log(e)}
  })
```
- jQuery `promise`写法：
```js
  jQuery.ajax({
    url:'/xxx',
    method: 'post',
    dataType:'json',
    data:'a=1&b=2',
  }).then(
    (e)=>{console.log(e)}, // 此时返回的是响应的responseText 的JSON.parse()的结果，即一个对象
    (e)=>{console.log('error')}
  )
```





