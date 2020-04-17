---
title: "用原生JavaScript实现类jQuery效果"
date: 2018-07-12
draft: false
categories: ["前端框架"] 
---

**前言**：众所周知，原生JS的DOM API大多都是很难用的，而在十二年前，已经有了一个很实用的库，它封装了很多好用的方法，让JavaScript更有易用性，如今全世界至少70%的网站都在使用，这个库就是jQuery。为了更容易理解jQuery的实现过程，这篇文章将用原生JS实现jQuery的元素选择、添加类、获取/设置文字这几个方法，当然jQuery的实际实现比这个要复杂的多，本文不做深入研究。

---

## 1、实现元素选择
- 首先我们需要认识到的是，jQuery实际上就是一个函数，所以最基本的结构应该是这样的：
```js
windos.jQuery = function(){
  
}

window.$ = window.jQuery // 实现类jQuery写法 $()
```
- 其次我们需要判断通过该函数传进来的参数是选择器还是DOM节点：是选择器的话，需要使用`document.querySelectorAll()`来获取元素，然后返回一个对象用来后续的操作；如果是DOM节点，为了保持操作的一致，也需要将其变为一个对象；如果先用一个变量获取DOM的集合，再将其以参数传入，也会返回一个对象（`var boxes = document.getElementsByTagName('div')` ，再将boxes传入，进入最后一个分支）。具体代码如下：
```js
window.jQuery = function(nodeOrSelector){
  let nodes = {}
  // 传入参数为选择器
  if (typeof nodeOrSelector === 'string'){
     let temp = document.querySelectorAll(nodeOrSelector)
     for(let i = 0 ; i < temp.length; i++){
        nodes[i] = temp[i]
     }
    nodes['length'] = temp.length
    
  }
  // 传入参数为节点
  else if(nodeOrSelector instanceof Node){
    nodes={
      0:nodeOrSelector,
      length:1
    }
  }
 // 传入参数为DOM节点集合
  else{
    for(let i = 0;i<nodeOrSelector.length;i++){
      nodes[i] = nodeOrSelector[i]
    }
    nodes['length'] = nodeOrSelector.length
  }
  
  return nodes
}
```

## 2、实现获取/设置元素的文本
封装一个`Text()`函数，操作的是转化过的`nodes`对象，如果不传参，则获取元素的文本，如果传参（参数需要是字符串），设置元素的文本为所传的参数（其中，如果元素是多个节点的集合，那么会为所有的元素都设置文本）。
```js
 nodes.Text = function(text){
    if(text === undefined){
      let texts = []
      for(let i = 0;i<nodes.length;i++){
       texts[i] = nodes[i].textContent
      }
      return texts
    }else{
      for(let i = 0;i<nodes.length;i++){
       nodes[i].textContent = text
      }
    }
  }
```

## 3、实现为元素添加/删除类

封装一个`addClass()`函数，操作的转化过的nodes对象，传入的参数需要是一个对象，对象的键名是需要操作的类，对象的键值是操作类型（如`{'red':true,'green':false}` 表示添加类名为`red`的类，同时删除类名为`green`的类）
- 首先使用`for in`循环遍历传入的参数，然后使用`for`循环遍历`nodes`节点，对每一个元素都要进行操作，判断键值是`true`还是`false`，前者则添加类，后者则删除类：
```js
nodes.addClass = function(classes){
    for(let key in classes){
      for(let i = 0;i<nodes.length;i++){
        if(classes[key]){
          nodes[i].classList.add(key)
        }else{
          nodes[i].classList.remove(key)
        }
      }  
    }
  }
```
观察上面的代码，发现关于`if`判断语句中的两个分支有相似的地方，而只要有相似的代码，就有优化的空间，所以进行下面的优化：
```js
nodes.addClass = function(classes){
    for(let key in classes){
      for(let i = 0;i<nodes.length;i++){
        let methodName = classes[key] ? 'add' : 'remove'
        nodes[i].classList[methodName](key)
      }  
    }
  }
```

## 4、完整代码
[代码链接](http://js.jirengu.com/vacucugotu/1/edit)

```js
window.jQuery = function(nodeOrSelector) {
  let nodes = {}
  if (typeof nodeOrSelector === 'string') {
    let temp = document.querySelectorAll(nodeOrSelector)
    for (let i = 0; i < temp.length; i++) {
      nodes[i] = temp[i]
    }
    nodes['length'] = temp.length
    console.log(1)

  } else if (nodeOrSelector instanceof Node) {
    nodes = {
      0: nodeOrSelector,
      length: 1
    }
    console.log(2)
  } else {
    for (let i = 0; i < nodeOrSelector.length; i++) {
      nodes[i] = nodeOrSelector[i]
    }
    nodes['length'] = nodeOrSelector.length
    console.log(3)
  }

  nodes.Text = function(text) {
    if (text === undefined) {
      let texts = []
      for (let i = 0; i < nodes.length; i++) {
        texts[i] = nodes[i].textContent
      }
      return texts
    } else {
      for (let i = 0; i < nodes.length; i++) {
        nodes[i].textContent = text
      }
    }
  }
  nodes.addClass = function(classes) {
    for (let key in classes) {
      for (let i = 0; i < nodes.length; i++) {
        let methodName = classes[key] ? 'add' : 'remove'
        nodes[i].classList[methodName](key)
      }
    }
  }


  return nodes
}

window.$ = window.jQuery
```


