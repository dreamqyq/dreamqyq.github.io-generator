---
title: "JavaScript中的函数防抖动与节流"
date: 2018-09-24
draft: false
categories: ["JavaScript"] 
tags: ["JavaScript"]
---

**摘要**：JavaScript 中的函数防抖和节流是很常用的需求，前端面试也是频率比较高被问到的知识，本文就通过二者的概念、实现思路、示例代码和应用场景来介绍一下它们。

## 概念
- **函数防抖**（debounce）：其概念其实是从机械开关和继电器的“去弹跳”（debounce）衍生出来的，基本思路就是把多个信号合并为一个信号。比如一个 `input` 每当输入结束后两秒执行搜索事件，这就是个很经典的防抖需求。

- **函数节流**（throttle）：节流的概念可以想象一下水坝，你建了水坝在河道中，不能让水流动不了，你只能让水流慢些。换言之，你不能让用户的方法都不执行。如果这样干，就是debounce了。为了让用户的方法在某个时间段内只执行一次，我们需要保存上次执行的时间点与定时器。

## 思路

- **函数防抖**的实现思路如下：将目标方法（动作）包装在setTimeout里面，然后这个方法是一个事件的回调函数，如果这个回调一直执行，那么这些动作就一直不执行。为什么不执行呢，我们设置了一个clearTimeout，这样setTimeout里的方法就不会执行！ 为什么要clearTimeout呢，我们就需要将事件内的连续动作删掉嘛！待到用户不触发这事件了。那么setTimeout就自然会执行这个方法。

-  **函数节流**的实现思路如下：某些代码不可以在没有间断的情况连续重复执行。第一次调用函数，创建一个定时器，在指定的时间间隔之后运行代码。当第二次调用该函数时，它会清除前一次的定时器并设置另一个。如果前一个定时器已经执行过了，这个操作就没有任何意义。然而，如果前一个定时器尚未执行，其实就是将其替换为一个新的定时器。目的是只有在执行函数的请求停止了一段时间之后才执行。

## 代码

### 函数防抖示例代码：
```js
function debounce(func, delay) {
    let timeout
    return function(e) {
        clearTimeout(timeout)
        let args = arguments
        timeout = setTimeout(() => {
          func.apply(this, args)
        },delay)
    }
}

var validate = debounce(function(e) {
    console.log( e.target.value)
}, 2000)

document.querySelector("input").addEventListener('input', validate)
```

### 函数节流示例代码：
```js
function debounce(func, delay) {
    let timeout
    return function(e) {
        clearTimeout(timeout)
        let args = arguments
        timeout = setTimeout(() => {
          func.apply(this, args)
        },delay)
    }
}

var validate = debounce(function(e) {
    console.log( e.target.value)
}, 2000)

document.querySelector("input").addEventListener('input', validate)
```

## 应用场景

### 防抖 debounce 多用于 “ 延迟多久后触发 ”
具体用于input输入框架的格式验证，假如只是验证都是字母也罢了，太简单了，不怎么耗性能，如果是验证是否身份证，这性能消耗大，你可以隔170ms才验证一次。这时就需要这个东西。或者你这个是自动完全，需要将已有的输入数据往后端拉一个列表，频繁的交互，后端肯定耗不起，这时也需要这个，如隔 350ms 。
所以大致就有以下两种应用情景：
- 每次 resize/scroll 触发统计事件
- 文本输入的验证（连续输入文字后发送 AJAX 请求进行验证，验证一次就好）

### 节流 throttle 多用于 “ 多久触发一次 ”
函数节流会用在比input, keyup更频繁触发的事件中，如resize, touchmove, mousemove, scroll。throttle 会强制函数以固定的速率执行。因此这个方法比较适合应用于动画相关的场景。
- DOM 元素的拖拽功能实现（mousemove）
- 射击游戏的 mousedown/keydown 事件（单位时间只能发射一颗子弹）
- 计算鼠标移动的距离（mousemove）
- Canvas 模拟画板功能（mousemove）
- 搜索联想（keyup）
- 监听滚动事件判断是否到页面底部自动加载更多：给 scroll 加了 debounce 后，只有用户停止滚动后，才会判断是否到了页面底部；如果是 throttle 的话，只要页面滚动就会间隔一段时间判断一次

## 相关链接
- [节流与防抖的区别](http://demo.nimius.net/debounce_throttle/)
- [函数防抖与函数节流——司徒正美](https://zhuanlan.zhihu.com/p/38313717)

（END）
