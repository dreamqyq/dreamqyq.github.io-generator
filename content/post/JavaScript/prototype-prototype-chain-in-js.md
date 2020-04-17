---
title: "初识传说中的原型与原型链"
date: 2018-06-15
draft: false
categories: ["JavaScript"] 
tags: ["JavaScript"]
---

**前言**：原型与原型链这两个名词毋庸置疑听起来是很高大上的，以前总是出现在传说中，这两天看了教程，准备介绍一下这传说中的原型以及原型链。本文将从全局对象引入，分别介绍Number、Boolean、String、Object四个对象，然后再详述原型的相关知识。

## 1、全局对象 window

首先我们还是先不说原型，先了解一下全局对象以及其中的window对象。
- **全局对象**（global object）：通俗的说就是在脚本的任意地方都可以调用的对象，在浏览器中即window对象。

**window对象**中，如alert()方法可以写成`window.alert()`，或者省去window，直接写做`alert()`。window对象分两种：
- 一种是ECMAScript规定必须得有的，比如parseInt 、 parseFloat，以及Number()  、Boolean() 、String() 、Object() 等。
- 第二种是各个浏览器有的，因为没有写如标准，所以这些方法在各个浏览器的表现形式都有所不同，比如：alert() 、prompt() 、confirm() 、console() 等方法以及 document 、history 等对象。

## 2、简单类型与对象

众所周知，如果我们想声明一个数值类型的变量，可以这么写：`var n1 = 1;`，这种写法声明的是一个简单类型；或者这么写：`var n2 = new Number(1);` ，这种写法下，n2 其实是对象，可以通过`n2.valueOf()`来获取该对象的值，可以用`n2.toString();`方法将其转换成字符串。

这两种写法所声明的变量最大的不同是，他们在**内存中的存储方式是不同的**，n1直接声明，那么他就是简单的数据类型，存储在stack内存中，而n2则是声明了一个对象，stack内存中存储着该对象的地址，对象的内容存储在heap内存中。

但是日常我们没有用n2的写法，依然可以使用valueOf() 方法 和toSting()方法，既然有简单的方法也可以不影响日常操作，为什么还要创建n1这种复杂的写法呢？很显然这里就隐藏着一段JS的黑历史（**为了让JavaScript看起来更像Java**），这里不细说，有兴趣的同学可以自行Google。

那么JS如何做到使用n1的写法还可以达到n2的用法呢？那是因为如果你调用了如valueOf方法，那么**JS会创建一个临时对象**，然后调用该对象的方法，**调用结束临时对象就被垃圾回收**。那么接下来介绍一个易错点在：
- `var num = 1; num.xxx = 2;` 这句话会报错吗?答案是不会，后一句为num添加属性时，JS已经为其创建了一个临时对象，为对象添加属性怎么会报错呢？
然后如果想读取xxx这个属性`num.xxx`，结果是**undefined**，因为为num添加完属性后，临时对象就被垃圾回收，num本质上还是一个数值，而数值本身是没有xxx这个属性的（有个这属性的对象已经被删了）。

## 3、Number对象

Number的常用属性有：
- `Number.valueOf(); ` 获取对象本身的值
- `Number.toString();` 将数值转化为字符串
注：number类型的的toString方法可以加参数，表示按照什么进制来解析（不加参数默认按十进制解析），如：
```js
(100).toString(); // "100"
(100).toString(2); // "1100100"
100..toString(16); // "64"
```
- `Number.toFixed();` 将其转换为小数，
如：`2..toFixed(3); // "2.000" `
- `Number.toExponential();` 将其转化为科学计数法，
如：`200..toExponential(); // "2e+2"`

## 4、String对象

String的常用属性有：
- `String.charAt();` 获取字符串中某一位的字符
如：`"hello world".charAt(1); // "e"`
- `String.charCodeAt();` 获取字符串中某一位的字符的Unicode编码
如：`"hello world".charCodeAt(1); // 101`
- `String.trim();` 删除字符串中多余的空格
如：`'  hello world    '.trim(); // "hello world"`
- `String1.concat(String2);` 连接字符串1和字符串2
如：`"hello".concat("world"); // "helloworld"`
- `String.slice(start,end);` 截取字符串，从start到end
如：`"hello world".slice(3,5); // "lo"`
- `String.replace('xx','yy');` 将字符串中的xx替换成yy(只能替换第一次出现的字符)
如：`"hello world".replace('o','a') // "hella world"`
- `String.indexOf();` 搜索字符串中的内容(只检测到第一次出现的字符)，没搜到返回-1
如：`"hello world".indexOf('o'); // 4`
`"hello world".indexOf('p'); // -1 `
- `String.split();` 分隔
如：`"hello world".split(' '); // ["hello", "world"]`
- `String.includes(xx);` 检查字符串中是否包含xx
如：`"hello world".includes('aa') ; //false`
- `String.substr(start，len);` 截取
如：`"hello world".substr(3,2); // "lo"`

## 5、Boolean对象

这里要介绍一个两种不同的赋值方法下容易出错的地方。
```js
var b1 = false;
var b2 = new Boolean(false);
if(b1) { console.log('b1') } ;
if(b2) { console.log('b2') } ;

///打印结果为 b2
```
上述代码之中，b1和b2的值都是false，而使用if判断语句，却将b2转化为了true，那是因为**b2本身的数据类型是对象**，而**对象不论是否是空对象，转化成boolean都是true**。

## 6、Object对象

Object对象，两种赋值方法是一样的，没有任何区别。
不过需要注意的是：
```js
var obj1 = {};
var obj2 = {};
obj1 === obj2; // false
```
上面的代码中，同样都是空数组，但是他们的值是不相等的，因为对象在stack内存中存储的只是其内容在heap内存中的地址，而每个对象的内容在heap内存中的地址是不会一样的，所以**对象与对象一般都是不相等的**。

## 7、公有属性 / 原型

那么说了这么多，接下来即将引入原型这个概念。
上面说了四种对象，而他们都有相同的一些属性，比如valueOf，toString等，难道JS每次声明一个对象都要写一次这些方法嘛？答案是否定的，因为真要这么写的话会非常占用内存，所以实际上JS引擎是这么做的：**把所有的对象共有的属性全部放在heap内存的一个地方，当需要使用的时候，引用这个对象即可**，这么操作就会减少不必要的内存浪费，而这个公有属性，就是传说中的**原型**。

- `__proto__`就是这些公有属性的引用。
- `Object.__proto__`指向的就是对象的公有属性。
- 而比如Number对象、String对象、Boolean对象，他们还有各自独有的公用属性，他们的`__proto__`指向的就是各自的独有属性，这些独有属性的`__proto__`指向对象的公有属性。
- 由此可知`obj1.toString === obj2.toString`这句话是正确的。

![示意图](https://upload-images.jianshu.io/upload_images/11827773-d90c821f88f65d5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

图中红色的线，就组成了一条**原型链**。

![](https://upload-images.jianshu.io/upload_images/11827773-2e0eb88bae640ee6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
JS引擎一出来就会把上图的这棵树画好让各个对象来引用。而浏览器一开始就有了原型对象，用`Object.prototype`指向原型，防止其因为没有对象引用而被回收。

则出现了一些等式：
- `obj.__proto__ === Object.prototype` 即对象的`__proto__`指向Object对象的`prototype`
- `num.__proto__  === Number.prototype` 即数值的`__proto__`指向Number对象的共有属性
- `num.__proto__.__proto__ === Object.prototype` 即Number对象的公有属性的`__proto__`指向Object对象的公有属性。
- 其他如Boolean对象、String对象和Number对象同理。

那么`__proto__`与`pprototype`到底代表着什么，有什么作用，区别是什么呢，请看下文。


## 8、`__proto__` 与 `prototype`

![](https://upload-images.jianshu.io/upload_images/11827773-a5e18b7bdfca00cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上图是我们还没有写代码的时候，浏览器内存中已经构建好了的内容。
由图可知，prototype是浏览器本身就写好的。

当我们写了一句代码`var s = new String(' hello ')` 以后：
![](https://upload-images.jianshu.io/upload_images/11827773-f439a3923632b7a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我们创建的对象的`__proto__` 会用来指向原有的String对象，使得我们可以调用String对象的公有属性。

于是我们有了以下结论：
- `__proto__` 是某对象公用属性的引用，是为了用户使用其共用属性中的方法而存在的 。
- `prototype` 是浏览器写的，本身就存在，是某对象的共同属性的引用，为了不让对象的公用属性因没有被调用而被垃圾回收而存在。


## 9、一些烧脑的等式

众所周知，**var 对象 = new 函数;**是JS中很基本的语句，如`var str = new String('aaa')`，因此就有了下面这句等式：
- `对象.__proto__ === 函数.prototype` ，即**对象的`__proto__` 指向了构造这个对象的函数的`prototype`**。 因此我们还知道了`__proto__` 是对象我的属性，`prototype`是函数的属性。

于是我们可以根据上述等式做一些推论：
- 首先我们知道，**函数的prototype是对象**，这个对象对应的就是最简单的函数Object，所以替换的到下面的式子：
`函数.prototype.__proto__ === Object.prototype `

- 由于函数本身即是函数（最优先视为函数），也是对象，而函数的构造函数是`Function` ，所以替换得：
`函数.__proto__ === Function.prototype `
- `Function`即是对象，也是函数，但他优先是个函数，所以将`Function`替换上面的式子，则变为： 
`Function.__proto__ === Function.prototype `
-  而`Function.prototype`也是对象，是普通的对象，所以其对应的函数使Object，替换得：
 `Funciton.prototype.__proto__=== Object.prototype ` 

## 10、神奇的`Function`

根据上面的各种推论，我们发现了其中最神奇的东西，`Function`，他即是函数，也是对象，所以他有函数的`prorotype`，也有对象的`__proto__`，所以出现了下图的情况：
![](https://upload-images.jianshu.io/upload_images/11827773-9c96a281cbc37a81.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

即`Function.prototype` 与`Funciton.__proto__`互相引用。
另外需要注意的一点：
` Object.__proto__ === Function.prototype`，因为 Function 是 Object 的构造函数。