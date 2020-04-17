---
title: "JS中的面向对象程序设计初识"
date: 2018-08-07
draft: false
categories: ["JavaScript"] 
tags: ["JavaScript"]
---

**前言**：面向对象程序设计（Object-oriented programming，简称OOP），是一种常见的编程思想。JavaScript 的核心是支持面向对象的，同时它也提供了强大灵活的 OOP 语言能力。本文从对面向对象编程的介绍开始，和您一起探索 JavaScript 的对象模型，最后描述 JavaScript 当中面向对象编程的一些概念。（本文部分文字摘自[知乎](https://www.zhihu.com/question/19854505)、[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript)、[阮一峰JS教程](http://javascript.ruanyifeng.com/oop/basic.html)）

## 1、什么是面向对象

- 比较正经的回答：
>把一组数据结构和处理它们的方法组成**对象**（object），把相同行为的对象归纳为**类**（class），通过类的**封装**（encapsulation）隐藏内部细节，通过**继承**（inheritance）实现类的**特化**（specialization）／**泛化**（generalization），通过**多态**（polymorphism）实现基于对象类型的**动态分派**（dynamic dispatch）。

- 抖机灵的回答：
![](https://upload-images.jianshu.io/upload_images/11827773-6cb7400488113fe0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript)中的介绍：
>面向对象编程是用抽象方式创建基于现实世界模型的一种编程模式。它使用先前建立的范例，包括模块化，多态和封装几种技术。
面向对象编程可以看作是使用一系列对象相互协作的软件设计。 在 OOP 中，**每个对象能够接收消息，处理数据和发送消息给其他对象**。每个对象都可以被看作是一个拥有清晰角色或责任的独立小机器。

- 可尝试理解MDN中介绍的面向对象的一些术语

>**Class 类**
定义对象的特征。它是对象的属性和方法的模板定义.
**Object 对象**
类的一个实例。
**Property 属性**
对象的特征，比如颜色。
**Method 方法**
对象的能力，比如行走。
**Constructor 构造函数**
对象初始化的瞬间, 被调用的方法. 通常它的名字与包含它的类一致.
**Inheritance 继承**
一个类可以继承另一个类的特征。
**Encapsulation 封装**
一种把数据和相关的方法绑定在一起使用的方法.
**Abstraction 抽象**
结合复杂的继承，方法，属性的对象能够模拟现实的模型。
**Polymorphism 多态**
多意为‘许多’，态意为‘形态’。不同类可以定义相同的方法或属性。

**其实面向对象最核心的四个概念就是：抽象、继承、封装、多态** 。

## 2、JavaScript中的面向对象编程

首先我们就要来了解一下原型编程，因为JS中实际上是弱类化的编程语言，是基于原型的而不是基于类的编程。
- 基于原型的编程不是面向对象编程中体现的风格，且行为重用（在基于类的语言中也称为继承）是通过装饰它作为原型的现有对象的过程实现的。这种模式也被称为弱类化，原型化，或基于实例的编程。
- 原始的（也是最典型的）基于原型语言的例子是由大卫·安格尔和兰德尔·史密斯开发的。然而，弱类化的编程风格近来变得越来越流行，并已被诸如JavaScript，Cecil，NewtonScript，IO，MOO，REBOL，Kevo，Squeak（使用框架操纵Morphic组件），和其他几种编程语言采用。

完整的内容可看[MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript#JavaScript_Object_Oriented_Programming) 关于JS 面向对象的介绍。本文仅做基础的介绍。

## 3、命名空间

命名空间是一个容器，它允许开发人员在一个独特的，特定于应用程序的名称下捆绑所有的功能。 在JavaScript中，命名空间只是另一个包含方法，属性，对象的对象。

**注意**：需要认识到重要的一点是：与其他面向对象编程语言不同的是，**Javascript中的普通对象和命名空间在语言层面上没有区别**。比如`var MySpace = {}`，从语言层面上，可以理解成创建了一个名为`MySpace`的对象，也可以理解为一个名为`MySpace`的命名空间，到底应该理解成哪一种主要是看你如何使用它。

创造的JavaScript命名空间背后的想法很简单：一个全局对象被创建，所有的变量，方法和功能成为该对象的属性。使用命名空间也最大程度地减少应用程序的名称冲突的可能性。

举例说明：
我们来创建一个全局变量叫做 `MYAPP`
`var MYAPP = MYAPP || {};`
在上面的代码示例中，我们首先检查`MYAPP`是否已经被定义（是否在同一文件中或在另一文件）。如果是的话，那么使用现有的`MYAPP`全局对象，否则，创建一个名为`MYAPP`的空对象用来封装方法，函数，变量和对象。

然后我们就可以创建子命名空间：
`MYAPP.event = {};`

## 4、类

**JavaScript是一种基于原型的语言**，它没类的声明语句，比如C+ +或Java中用的。相反，JavaScript可用方法作类。定义一个类跟定义一个函数一样简单。在下面的例子中，我们定义了一个新类Person。
```js
function Person() { } 
// 或
var Person = function(){ }
```
**对象**（类的实例）
我们使用 `new obj` 创建对象` obj` 的新实例, 将结果（obj 类型）赋值给一个变量方便稍后调用。举例说明：
在下面的示例中，我们定义了一个名为`Person`的类，然后我们创建了两个`Person`的实例(`person1` 和 `person2`).
```js
function Person() { }
var person1 = new Person();
var person2 = new Person();
```

## 5、构造函数

面向对象编程的第一步，就是要生成对象。前面说过，对象是单个实物的抽象。通常需要一个模板，表示某一类实物的共同特征，然后对象根据这个模板生成。

典型的面向对象编程语言（比如 C++ 和 Java），都有“类”（class）这个概念。所谓“类”就是对象的模板，对象就是“类”的实例。但是，**JavaScript 语言的对象体系，不是基于“类”的，而是基于构造函数（constructor）和原型链（prototype）**。

JavaScript 语言使用**构造函数**（constructor）作为对象的模板。所谓”构造函数”，就是**专门用来生成实例对象的函数**。它就是对象的模板，描述实例对象的基本结构。一个构造函数，可以生成多个实例对象，这些实例对象都有相同的结构。

构造函数就是一个普通的函数，但是有自己的特征和用法。如：
```js
var Vehicle = function () {
  this.price = 1000;
};
```
上面代码中，`Vehicle`就是构造函数。**为了与普通函数区别，构造函数名字的第一个字母通常大写**。

构造函数的特点有两个：
- 函数体内部使用了`this`关键字，代表了所要生成的对象实例。
- 生成对象的时候，必须使用`new`命令。

下面将详细介绍一下`new`命令和`this`关键字。

## 6、`new` 命令
写之前先放一个链接，是方大写的[关于new的文章](https://zhuanlan.zhihu.com/p/23987456)，私以为能解决很多人关于new的疑惑。

`new`命令的作用，就是执行构造函数，返回一个实例对象。如：
```js
var Vehicle = function () {
  this.price = 1000;
};
var v = new Vehicle();
v.price // 1000
```
上面代码通过new命令，让构造函数Vehicle生成一个实例对象，保存在变量v中。这个新生成的实例对象，从构造函数Vehicle得到了price属性。new命令执行时，构造函数内部的this，就代表了新生成的实例对象，this.price表示实例对象有一个price属性，值是1000。

`var v = new Vehicle();`使用`new`，JS默默的做了哪些事情呢：
- 创建临时对象
- 将这个临时对象赋值给函数内部的`this`关键字。
- `Viehicle.prototype = { constructor: 构造函数 }`
- `临时对象.__proto__ = Vehicle.prototype`
- 执行 `Vehicle.apply(this,arguments)`
- `return this` 即 `return 创建的临时对象`


![来自方方的示意图](https://upload-images.jianshu.io/upload_images/11827773-f46151a4a4671b9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 7、`this` 关键字

关于`this`关键字，我之前写的一篇[关于函数的博客](https://www.jianshu.com/p/13c8389a3c72)初步的介绍过，本文将再次详细的了解一下这个看起来有点坑的事物。（这里也推荐一篇方大写的[介绍`this`的博客](https://zhuanlan.zhihu.com/p/23804247) ，下面的内容也借鉴了该博客所说的思路）

- 什么是`this`，最本质的概念是：**`this`就是你 `call` 一个函数时，传入的第一个参数。**
- 怎么判断`this`到底指的是什么，将函数的调用形式转换为 call 形式即可。
- 如果是一些封装过的函数（比如`onclick()、addEventListener()`等），如何判断`this`
  1. 看源码中对应的函数是怎么被 `call` 的（这是最靠谱的办法）
  2. 看文档
  3. `console.log(this)`
  4. **不要瞎猜，你猜不到的**

举例说明：
- 首先来看如何将普通的函数调用转换为`call`的形式：
```js
func(p1, p2) // 等价于
func.call(undefined, p1, p2)

obj.child.method(p1, p2) // 等价于
obj.child.method.call(obj.child, p1, p2)
```
- 下面看几个例子。
例1：
```js
function func(){
  console.log(this)
}
func() // 转化为下面的句子
func.call(undefined) // 可以简写为 func.call()
```
此时`this`即为`undefined`，但注意：
**如果你的call传的第一个参数是 `null` 或者 `undefined`，那么 `window` 对象就是默认的`this`（严格模式下默认为 `undefined`）**

- 例2：
```js
var obj = {
  foo: function(){
    console.log(this)
  }
}
obj.foo() // 转化为下面的语句
obj.foo.call(obj)
```
本例中的`this`即为`obj`

- 例3：
```js
function fn (){ console.log(this) }
var arr = [fn, fn2]
arr[0]() // 这里面的 this 又是什么呢？
```

我们可以把 `arr[0]( )` 想象为`arr.0( )`，虽然后者的语法错了，但是形式与转换代码里的 `obj.child.method(p1, p2)`对应上了，于是就可以愉快的转换了：
```
arr[0]() 
假想为    arr.0()
然后转换为 arr.0.call(arr)
那么里面的 this 就是 arr 了 :)
```

- 例4：（下面三个例子都是一些常见的经过封装后的函数的`this`，这些函数就无法转化成`call`的形式了，需要看文档才能知道，我帮大家看了文档，所以下面的例子就需要单独记忆啦）
```js
button.onclick = function(){
  console.log(this)
}
// 这里的 this 指的是 触发事件的元素 即 button
```

- 例5：
```js
button.addEventListener('click',function(){
  console.log(this)
})
// 这里的 this 指的是 触发事件的元素的引用 即 button
```

- 例6：（jQuery）
```js
$('ul').on('click','li',function(){
  console.log(this)
})
// 这里的 this 指的是 正在执行事件的元素 即 li
```

- 例7：下面三个例子就比较绕啦
```js
function X() {
  return obj = {
    name:'obj',
    fn1(x) {
        x.fn2()
      },
      fn2() {
        console.log(1)
        console.log(this) // A
      }
  }
}
var options = {
  name:'options',
  fn1() {},
    fn2() {
      console.log(2)
      console.log(this) // B
    }
}
var x = X()
x.fn1(options)
```
问：这段的代码执行的是A还是B，打印出来的`this`指的是什么（不公布答案，自己思考后可在控制台验证结果）

- 例8：
```js
function X() {
  return obj = {
    name:'obj',
    fn1(x) {
        x.fn2.call(this)
      },
      fn2() {
        console.log(1)
        console.log(this) // A
      }
  }
}
var options = {
  name:'options',
  fn1() {},
    fn2() {
      console.log(2)
      console.log(this) // B
    }
}
var x = X()
x.fn1(options)
```
问题同上。

- 例9：
```js
function X() {
  return obj = {
    name: 'obj',
    options: null,
    fn1(x) {
      this.options = x
      this.fn2()
    },
    fn2() {
      console.log(1)
      this.options.fn2.call(this) // A
    }
  }
}
var options = {
  name: 'options',
  fn1() {},
  fn2() {
    console.log(2)
    console.log(this) // B
  }
}
var x = X()
x.fn1(options)
```
问题同上。


## 8、做几个小练习吧

1. 补全下面的代码：
```js
function Human(options){

} // 构造函数结束

Human.prototype.______ = ___________
Human.prototype.______ = ___________
Human.prototype.______ = ___________

var human = new Human({name:'Frank', city: 'Hangzhou'})
var human2 = new Human({name:'Jack', city: 'Hangzhou'})
```
  - 补全代码，使得 human 对象满足以下条件：
    - `human` 这个对象本身具有属性 `name` 和 `city`
    - `human.__proto__ `对应的对象（也就是原型）具有物种（species）、走（walk）和使用工具（useTools）这几个属性
    - `human.__proto__.constructor === Human` 为 true

human2 和 human 类似。

  - 参考答案：
```js
function Human(options){
    this.name = options.name
    this.city = options.city

} // 构造函数结束

Human.prototype.species = 'Human'
Human.prototype.walk = function(){}
Human.prototype.useTools = function(){}

var human = new Human({name:'Frank', city: 'Hangzhou'})
var human2 = new Human({name:'Jack', city: 'Hangzhou'})
```

2.  填空（本题不给答案，不确定的可以直接在控制台测试）：

```js
var object = {}
object.__proto__ ===  ????填空1????  // 为 true

var fn = function(){}
fn.__proto__ === ????填空2????  // 为 true
fn.__proto__.__proto__ === ????填空3???? // 为 true

var array = []
array.__proto__ === ????填空4???? // 为 true
array.__proto__.__proto__ === ????填空5???? // 为 true

Function.__proto__ === ????填空6???? // 为 true
Array.__proto__ === ????填空7???? // 为 true
Object.__proto__ === ????填空8???? // 为 true

true.__proto__ === ????填空9???? // 为 true

Function.prototype.__proto__ === ????填空10???? // 为 true
```

3. 在 ES5 中如何用函数模拟一个类？
- 参考答案：
  ES 5 没有 class 关键字，所以只能使用函数来模拟类。代码如下：
  ```js
  function Human(name){
    this.name = name
  }
  Human.prototype.run = function(){}

  var person = new Human('enoch')

  ```



