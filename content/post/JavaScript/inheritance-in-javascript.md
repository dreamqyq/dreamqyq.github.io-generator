---
title: "JavaScript中的继承"
date: 2018-09-22
draft: false
categories: ["JavaScript"] 
tags: ["JavaScript"]
---

**摘要**：继承是面向对象思想中的重要概念，虽然严格来说 JavaScript 并属于面向对象类型的语言，但最终还是在ES 6 中正式引入了继承的概念。本文就来介绍一下继承的概念、以及在 ES5 和 ES6 中两种写继承的方法。

## 面向对象中的继承

话不多说，先来看一下维基百科的解释
>**继承**（ inheritance）是面向对象软件技术当中的一个概念。如果一个类别B“继承自”另一个类别A，就把这个B称为“A的子类”，而把A称为“B的父类别”也可以称“A是B的超类”。**继承可以使得子类具有父类别的各种属性和方法**，而不需要再次编写相同的代码。

所以我们知道了**继承是类和类之间的关系，它使得子类具有了父类别的各种属性和方法**。所以继承这个概念本身是属于面向对象编程的，只有面向对象编程里面才有类的概念。
然而 JavaScript 是函数式编程，根本就没有类的概念，那么 JS 中的继承的概念从何谈起呢？

## JavaScript 中的继承

在说 JS 的继承之前，我们得先了解原型链的概念，简单来说就是每一个对象都有一个`__proto__` 属性，指向该对象的原型 `prototype`，原型的层层指向就形成了一个类似链表的结构（实际上原型的指向是多对一的，所以把这种结构说成“树”更准确一点）称之为原型链。

>JavaScript 对象是动态的属性“包”（指其自己的属性）。JavaScript 对象有一个指向一个原型对象的链。当试图访问一个对象的属性时，它不仅仅在该对象上搜寻，还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。

**而 JavaScript 中的继承就是基于原型链的继承**。
由于JS里面没有类，只有函数，所以我们只好这么称呼：子类函数、父类函数（比如 `Array` 与 `Object` 的关系），因此可以这么解释继承：子类函数构造出来的对象直接拥有父类的属性。  

因此，我们要写出继承，还得先写一个类，那么类到底是什么呢：**能产生对象的东西即为类**，那么，下面的代码中，`Human` 就可以称之为类。`person` 就是一个有自己属性的对象。
 ```js
function Human(name){
    this.name = name
}
var person = new Human('Enoch')
```
如果我们想在它的原型链上加一个属性，就可以这么写：
```js
Human.prototype.run = function(){}
```
现在 `person` 的原型结构就是这样的：
![](https://upload-images.jianshu.io/upload_images/11827773-b4eaebff27341090.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

有了类，下面就可以来写继承了，我们要实现一个类 `Man` 继承自 `Human`，他有自己的属性，同时也继承了 `Human` 的属性和方法。

## ES 5 中的继承写法

```js
 function Human(name){
     // 自身的属性 
     this.name = name
 }
// 原型链第一层的属性
 Human.prototype.run = function(){
     console.log("我叫"+this.name+"，我在跑")
     return undefined
 }
// 声明一个 Man 类
 function Man(name){
     // 使得 Man 类有了 Human 类 第一层的 name 属性
     // 即实现了对象本身属性的添加 
     Human.call(this, name)
     this.gender = '男'
 }

 // 接下来要实现 Man 也拥有 Human 第二层的方法 run
 // 最简单的方式是：
 // Man.prototype.__proto__ = Human.prototype
 // 但是上一句不符合 ECMA 标准，因此要写下面三行
 var f = function(){}
 f.prototype = Human.prototype
 Man.prototype = new f()

 // 此为 Man 类自身具有的方法，处于原型链第二层
 Man.prototype.fight = function(){
     console.log('糊你熊脸')
 }
```
写好以后，声明一个 `person` 对象： `var person = new Man('Enoch')`
此时  `person` 的结构如下：
![](https://upload-images.jianshu.io/upload_images/11827773-47b2f57171f11d45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们来理一下 person 对象的原型链
```js
person.__proto__ === Man.prototype
person.__proto__.__proto__ === Human.prototype
person.__proto__.__proto__.__proto__ === Object.prototype
person.__proto__.__proto__.__proto__.__proto__ === null
```

## ES 6 中的继承写法

需要注意的是，ES 6 虽然新增了 `class` 语法，但它本质上只是一个语法糖而已，JavaScript 目前为止仍然是基于原型链的继承，没有真正的类的概念。

```js
 class Human{
     constructor(name){
         this.name = name
     }
     run(){
         console.log("我叫"+this.name+"，我在跑")
         return undefined
     }
 }
 // extends 关键字 等价于 
 // Man.prototype.__proto__ = Human.prototype
 class Man extends Human{
     constructor(name){
         // super(name) 等价于 Human.call(this,name)
         super(name)
         this.gender = '男'
     }
     fight(){
         console.log('糊你熊脸')
     }
 }
```

## ES 6 写法的不足
看了上一节，我们会发现 ES 6 写起来好爽，代码简洁了很多，而且曾经写过 Java 的同学还会觉得分外亲切，因为这种写法就是 JavaScript 借( chao )鉴( xi )了 Java 的类的继承的操作。但是这么写还有一个需求不太容易实现。
比如我们需要在类的原型上写一个不是函数的属性，比如 `Human` 的原型上有一个 种族 `race` 的属性， 就不能直接写，如要如下操作：
```js
class Human {
  constructor(name){
    this.name = name
  }
  get race(){
    return '人类'
  }
  run(){}
}
```
然后一个`person = new Human('Enoch')` 就呈现了如下结构 

![](https://upload-images.jianshu.io/upload_images/11827773-f7728b0b655f59d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

而我们如果用 ES 5 的写法就很容易：
```js
function Human(name){
   this.name = name
}
Human.prototype.race = '人类'
```
`person` 的结构如下：

![](https://upload-images.jianshu.io/upload_images/11827773-18458b6c119214f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

（END）
