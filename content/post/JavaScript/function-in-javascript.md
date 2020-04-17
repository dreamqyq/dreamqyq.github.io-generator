---
title: "认识JavaScript中的函数"
date: 2018-07-10
draft: false
categories: ["JavaScript"] 
tags: ["JavaScript"]
---

**前言**：本文将详细的介绍JS中函数的相关概念（包括函数的`call stack` 、`this` 、作用域、闭包、柯里化、高阶函数等），总结函数中比较容易理解错的坑，让我们更加全面的认识函数。部分概念与代码参考[阮一峰JS教程](http://javascript.ruanyifeng.com/grammar/function.html)。

## 1、什么是函数

>函数是一段可以反复调用的代码块。函数还能接受输入的参数，不同的参数会返回不同的值。

函数的本质就是对象，或者说可以执行代码的对象就是函数。
因此我们甚至可以使用写纯对象的方式来写一个函数，让我们更了解函数的本质：
```js
var fn = {} ;
fn.params = ['x','y'] ; // 参数
fn.fbody = 'console.log(1)' ; //函数体
fn.call = function(){
	eval(fn.fbody)
}
fn.call() ; // 调用函数的call方法来执行函数
// 1
```
如上述代码，fn作为一个对象，使用`call()`方法执行的效果和执行函数是一样的，当然fn只是个长得像函数的对象，只是为了便于我们理解函数的本质。

注：上面代码中使用了`eval()` 方法，那么就在这里提一下：
 `eval`命令的作用是，将字符串当作语句执行。`eval()` 方法是`window`的全局对象。
如`eval('var a = 1;'); a // 1`
一般情况下尽量避免使用该方法。

## 2、函数的五种声明方法

注：函数体内部的`return`语句，表示返回。JavaScript 引擎遇到`return`语句，就直接返回`return`后面的那个表达式的值，后面即使还有语句，也不会得到执行。也就是说，`return`语句所带的那个表达式，就是函数的返回值。`return`语句不是必需的，如果没有的话，该函数就不返回任何值，或者说返回`undefined`。

- 具名函数的声明
 ```js
 function fn1() {
    return undefined; // 如果不写return ，浏览器默认添加
}  
```

- 匿名函数的声明
`var fn2 = function() {}`

- 结合上面两种方式(谨慎使用)
 `var fn3 = function fn4() {}`

- 使用`Function`函数对象来声明
```js
var fn5 = new Function(
  'x',
  'y',
  'return x + y'
);
```
其中`new Function()` 中，最后一个参数表示函数体，前面的参数表示传入函数的参数。

- 最酷炫的声明方法：箭头函数（详情可见本文第14条）
`var fn6 = (x,y) => {return x+y}`箭头前面表示传入函数的参数，箭头后面表示函数体。
如果只有一个参数，参数的圆括号可以省略：
` var fn7 = x => {return x*2} `
如果函数体只有一句话，可以同时省略函数体的大括号及`return`：
` var fn8 = x => x*x `


## 3、函数的调用

众所周知，函数的调用很简单，比如有这样一个函数
`function fn (){ return undefined; }` 
我们只需` fn(); ` 即可执行该函数。

但是作为初学者，更建议使用`call()`方法，比如有函数：
`function fn(x,y){ return x+y; }`
就可以这么调用：`fn.call(undefined,1,2) // 结果为3` 等价于这么写`fn(1,2)`
之所以建议初学者使用`call()`方法，因为这么写更便于理解`this`和`arguments`
比如上面的那句调用，`call()`的第一个参数就是`this`，后面的参数就是`arguments`

## 4、函数的常用属性和方法

- `name` 属性
每一个函数都有`name`属性，你以为`name`属性就真的是表示函数的名字而且很好理解吗？
明显答案是否定的，使用不同的函数声明方式，其`name`属性的值可能就不是你想象中的样子。下面举例说明：
```js
// 具名函数的name属性 ，表示函数的名字
function fn1() {} 
fn1.name // "fn1"

// 匿名函数的name属性，指的是接收函数的变量名
var fn2 = function () {} 
fn2.name // "fn2"

// 下面这种声明方法，在外部获取不到fn4函数
// 注：fn3.name返回函数表达式的名字。注意，真正的函数名还是fn3，而fn4这个名字只在函数体内部可用。
var fn3 = function fn4(){} 
fn3.name // "fn4"

// 使用Function() 方法构造函数，函数的name属性值为"anonymous"
var fn5 = new Function()
fn5.name // "anonymous"

// 箭头函数的name属性，指的也是接收该函数的变量名
var fn6 = () => {}
fn6.name // "fn6"
```

- `length` 属性
函数的`length`属性返回函数预期传入的参数个数，即函数定义之中的参数个数。
如：
```js
function f(a, b) {}
f.length // 2
```
上面代码定义了空函数f，它的length属性就是定义时的参数个数。不管调用时输入了多少个参数，length属性始终等于2。
注：`length`属性提供了一种机制，判断定义时和调用时参数的差异，以便实现面向对象编程的”方法重载“（overload）。

- `toString()` 方法
函数的`toString`方法返回一个字符串，内容是函数的源码，包括函数中的注释也会被打印出来。如：
```js
function f() {
  var a = 1
/*  这是一个
  多行注释*/
}

f.toString()
//   "function f() {
//     var a = 1
//   /*  这是一个0
//     多行注释*/
//   }"
```

## 5、`call` & `apply` & `bind` 的用法
在 javascript 中，`call` 和 `apply` 都是为了改变某个函数运行时的上下文（context）而存在的，换句话说，就是为了改变函数体内部 this 的指向。
- `call()` 方法调用一个函数, 其具有一个指定的`this`值和分别地提供的参数(参数的列表)。

- `apply()` 方法调用一个函数, 其具有一个指定的`this`值，以及作为一个数组（或类似数组的对象）提供的参数。

- `bind()` 方法创建一个新的函数，被调用时，将其`this`关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。

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




## 6、call stack 调用栈
>"调用栈"（call stack）表示函数或子例程像堆积木一样存放，以实现层层调用。
举个函数嵌套调用的例子：
```js
function a(){
    console.log('a1')
    b.call()
    console.log('a2')
  return 'a'  
}
function b(){
    console.log('b1')
    c.call()
    console.log('b2')
    return 'b'
}
function c(){
    console.log('c')
    return 'c'
}
a.call()
console.log('end')
```
其执行顺序如图所示：
![](https://upload-images.jianshu.io/upload_images/11827773-cad795aa0189b508.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面是嵌套调用和递归调用时，函数的执行顺序：
*   [嵌套调用](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gYSgpewogICAgY29uc29sZS5sb2coJ2ExJykKICAgIGIuY2FsbCgpCiAgICBjb25zb2xlLmxvZygnYTInKQogIHJldHVybiAnYScgIAp9CmZ1bmN0aW9uIGIoKXsKICAgIGNvbnNvbGUubG9nKCdiMScpCiAgICBjLmNhbGwoKQogICAgY29uc29sZS5sb2coJ2IyJykKICAgIHJldHVybiAnYicKfQpmdW5jdGlvbiBjKCl7CiAgICBjb25zb2xlLmxvZygnYycpCiAgICByZXR1cm4gJ2MnCn0KYS5jYWxsKCkKY29uc29sZS5sb2coJ2VuZCcp!!! "null")
*   [递归调用](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gc3VtKG4pewogICAgaWYobj09MSl7CiAgICAgICAgcmV0dXJuIDEKICAgIH1lbHNlewogICAgICAgIHJldHVybiBuICsgc3VtLmNhbGwodW5kZWZpbmVkLCBuLTEpCiAgICB9Cn0KCnN1bS5jYWxsKHVuZGVmaW5lZCw1KQ%3D%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D "null")

## 7、函数中的`this` 和 `arguments`
（本文只介绍this和arguments是什么，暂时不介绍他们的用法，关于`this`更深入的认识，可见我的另一篇[关于JS面向对象的博客](https://www.jianshu.com/p/f682c3a59692) 第7条）
- 首先举个例子：
声明 `function fn(x,y){ return x+y }`
调用 ` fn.call( undefined , 1 ,2 ) `
其中 ，`undefined` 指的就是函数fn的`this` ，即，`this`是`call()`的第一个参数。`1,2` 指的就是函数的`arguments`，需要注意的一点是：`arguments`是一个类数组对象。

- __注意：在普通模式下，this是undefined时，浏览器会将其变为`window`
在严格模式下，`this` 是 `undefined` ，打印出来的就是`undefined`__ 。
- 举例：
```js
// 普通模式下：
function f(){
    console.log(this)
}
f.call(1)  // Number {1}

// 严格模式下：
function f(){
    'use strict'
    console.log(this)
}
f.call(1)  // 1
```
即，**普通模式下，浏览器会把`this` 转化为对象，严格模式下会禁止这样的转换**。
- **注意**：`this`必须是对象，或者这么说，__`this`就是函数与对象之间的羁绊__。
举个例子：`fn.call(10)`，意思是想让函数`fn`的`this`是数字10，但实际上
JS会将其变成一个数字对象，即`Number(10)`。

## 8、词法作用域（也叫静态作用域）

> 作用域（scope）指的是变量存在的范围。在 ES5 的规范中，Javascript 只有两种作用域：一种是全局作用域，变量在整个程序中一直存在，所有地方都可以读取；另一种是函数作用域，变量只在函数内部存在。ES6 又新增了块级作用域。

规则：
**按照抽象语法树，就近原则**
**注意：我们只能确定变量是哪个变量，但是不能确定变量的值**

在判断作用域时，还要注意函数作用域内部会产生的“变量提升”现象。var命令声明的变量，不管在什么位置，变量声明都会被提升到函数体的头部。

## 9、什么是闭包
（本节只介绍闭包的概念，暂不深入，这里提供[方方的一篇介绍闭包的文章](https://zhuanlan.zhihu.com/p/22486908)用于深入了解，或可见我写的一篇[关于前端基础知识十题](https://www.jianshu.com/p/1a087a8bd219)中的第四题）

如果一个函数使用了它范围外的变量，那么**（这个函数和这个变量）**就叫做**闭包**。

闭包（closure）是 Javascript 语言的一个难点，也是它的特色，很多高级应用都要依靠闭包实现。
理解闭包，首先必须理解变量作用域。前面提到，JavaScript 有两种作用域：全局作用域和函数作用域。函数内部可以直接读取全局变量。

## 10、柯里化

**柯里化**是一个听起来很高大上的概念，但也很好理解。简单来说就是一个返回函数的函数，然后将函数的参数中的一个或几个参数确定下来，如将 f(x,y) 变成 f(x=1)(y) 或 f(y=1)x，柯里化可以将真实计算拖延到最后再做。这里贴两个详细介绍柯里化的文章：[http://www.yinwang.org/blog-cn/2013/04/02/currying](http://www.yinwang.org/blog-cn/2013/04/02/currying "null") 、[https://zhuanlan.zhihu.com/p/31271179](https://zhuanlan.zhihu.com/p/31271179 "null")

- 举两个例子：
```js
  // 第一个例子：
  // 柯里化之前
  function sum(x,y){
      return x+y
  }
  // 柯里化之后
  function addOne(y){
      return sum(1, y)
  }

  // 第二个例子：
  // 柯里化之前
  function Handlebar(template, data){
      return template.replace('{{name}}', data.name)
  }
  // 柯里化之后
  function Handlebar(template){
      return function(data){
          return template.replace('{{name}}', data.name)
      }
  }
```
上面的代码中，第一个例子好理解，我们来看第二个例子。
柯里化之前，我们要调用函数`Handlebar`，每次都要传一个template模板，比如`Handlebar('<h1>I'm 
 {{name}}</h1>',{name:'noch'})` ，如果这个一个模板（即参数`template`为`'<h1>I'm  {{name}}</h1>'`） 我们需要用到很多次，难道每次调用都要赋一次值吗？为了简化函数`Handlebar`的这种情况下的调用，我们就对该函数进行了柯里化。之后再使用时，首先这么调用`var newHandlebar= Handlebar('<h1>I'm {{name}}</h1>')` ，相当于`给template`赋了值，然后调用函数`newHandlebar`：`newHandlebar({name:'enoch'})` 即可

- 一道关于柯里化的题目：
请写出一个柯里化其他函数的函数 curry，这个函数能够将接受多个参数的函数，变成多个接受一个参数的函数，具体见示例（这是 lodash.curry 的文档示例）：
```js
function curry(???){
    ???
    return ???
}
var abc = function(a, b, c) {
  return [a, b, c];
};

var curried = curry(abc);

curried(1)(2)(3);
// => [1, 2, 3]

curried(1, 2)(3);
// => [1, 2, 3]

curried(1, 2, 3);
// => [1, 2, 3]
```
答案如下：
```js
function curry(func , fixedParams){
    if ( !Array.isArray(fixedParams) ) { fixedParams = [ ] }
    return function(){
        let newParams = Array.prototype.slice.call(arguments); // 新传的所有参数
        if ( (fixedParams.length+newParams.length) < func.length ) {
            return curry(func , fixedParams.concat(newParams));
        }else{
            return func.apply(undefined, fixedParams.concat(newParams));
        }
    };
}
```


## 11、高阶函数

- 在数学和计算机科学中，高阶函数是至少满足下列一个条件的函数（也就是说__至少满足下列一个条件的函数就被称为高阶函数__）：
  - 接受一个或多个函数作为输入：如：`forEach` 、`sort`、 `map`、 `filter` 、`reduce`
  - 输出一个函数：如：`bind` 、`lodash.curry`
  - 不过它也可以同时满足两个条件：如：`Function.prototype.bind`
- 那么高阶函数有什么用呢：最重要的一条就是可以将函数任意的组合。（如react中的很多应用）

## 12、回调（`callback`）

- 名词形式：被当做参数的函数就是回调
- 动词形式：调用这个回调
- 举个例子：
`fn(function(){})` ：函数`fn`中的参数是一个函数，在`fn`中调用了这个函数，那么这个函数就是**回调函数**，调用的过程就是**回调**。
- **注意**：回调跟异步没有任何关系

## 13、构造函数

简单的来说**返回对象的函数就是构造函数** 。（具体用法可看我的一篇[介绍面向对象的博客](https://www.jianshu.com/p/f682c3a59692) 第五条）。

比如`new Number()` 得到的就是一个数值对象，一般的，**构造函数的函数名首字母要大写**。

再举个栗子，我们自己写一个构造函数。
```js
function Person(){} // 这就是个构造函数，何以见得，就要看你如何调用
var person1 = Person() // 这么写的话，函数Person什么也没有返回
var person2 = new Person() //使用new，函数Person中默认会多出几行，其中就包括会返回一个空对象

// 如果想在函数中添加属性，可以使用this，即：
function Person(){
  this.name = ''
  return this // 这一行可以不用写
}
var person3 = new Person({}) // 这么调用即可，会为传入的空对象增加一个name的属性
```

## 14、箭头函数

箭头函数的形式在本文第2条已经介绍，这里不再赘述。接下来主要介绍一下箭头函数和普通的函数的区别：
- **箭头函数没有`this`**，对，这就是它们之间唯一的区别。具体的说就是：箭头函数中如果使用了`this`，此时`this`就是相当于一个普通的参数，由于箭头函数本身没有`this`，那么他就会找它的父级中的`this`来确定`this`的值。
- 举个例子：
```js
setTimeout(function(){
	console.log(this)
}.bind({name:'enoch'}),1000)
// 一秒后打印出 {name: "enoch"}
// 如果没有用bind绑定this ，打印出的是window对象
```
```js
setTimeout(function(){
	console.log(this) // A
	setTimeout(function(){
		console.log(this) // B
	},1000)
}.bind({name:'enoch'}),1000)
// 第一秒打印出 {name: "enoch"}，第二秒打印出window对象
```
第二个代码块中：`setTimeout`中又有一个`setTimeout`，两个`setTimeout`都会执行打印`this`这句话，而A处的`this`和B处的`this`显然不是一个值（A处的`this`为`{name: "enoch"}`，B处的 `this`为`window`对象）。
如果你想让两个`this`都是`{name: "enoch"}`，可以这么做，里面的函数也用`bind`绑定外面的`this`：
```js
setTimeout(function(){
    console.log(this) // A
    setTimeout(function(){
        console.log(this) // B
    }.bind(this),1000)
}.bind({name:'enoch'}),1000)
```
或者使用箭头函数
```js
setTimeout(function(){
    console.log(this) // A
    setTimeout(()=>{
        console.log(this) // B
    },1000)
}.bind({name:'enoch'}),1000)
```
那么如果我硬要用`call`来指定箭头函数的`this`可以嘛，答案是否定的，请看代码：
```js
var fn = ()=>{console.log(this)}
fn() // 此时打印出的是window对象
fn.call({name:'enoch'}) 
// 此时打印出来的还是window对象，箭头函数没有接收你指定的this
```
所以使用箭头函数可以很容易的做到函数里面的`this`就是外面的`this`，不用担心函数的`this`会莫名的改变。


## 15、一些易错的坑
看了这么多概念，觉得函数貌似也不太难呢？嘿嘿，那么请继续，下面将写一些函数中易错的坑。
- 题目1：求f1.call()的结果
```js
var a = 1
function f1(){
    alert(a) // 是多少
    var a = 2
}
f1.call()
```

- 题目2：
 ```js
var a = 1
function f1(){
    var a = 2
    f2.call()
}
function f2(){
    console.log(a) // 是多少
}
f1.call()
```

- 题目3：点击第3个 li 时，打印 2 还是打印 6？
```js
var liTags = document.querySelectorAll('li')
for(var i = 0; i<liTags.length; i++){
    liTags[i].onclick = function(){
        console.log(i) // 点击第3个 li 时，打印 2 还是打印 6？
    }
}
```

- 题目4：这两个代码块的结果一样吗？
```js
function f(){
    console.log(this)
}
f.call(1)
```
```js
function f(){
    'use strict'
    console.log(this)
}
f.call(1)
```

- 题目5：下面的几个代码块，a的值分别为什么
```js
var a = console.log(1);
```
```js
function f(){
    return 1
}
a = f
```
```js
function f(){
    return 1
}
var a = f.call()
```

- 题目6：下面的几个代码块，a的值分别为什么
```js
var a = 1,2
```
```js
var a = (1,2)
```
```js
var a = (1, console.log(2))
```

- 题目7：
```js
function f(){
    return function f2(){}
}
var a = f.call()
```
```js
function f(){
    return function f2(){}
}
var a = f.call()
var b = a.call()
```
```js
function f(){
    return function f2(){}
}
var a = f.call().call()
```

- 题目8：
```js
function f1(){
    console.log(this)
    function f2(){

    }
}
var obj = {name: 'obj'}
f1.call( obj )
```
```js
function f1(){

    function f2(){
        console.log(this)
    }
    f2.call()
}
var obj = {name: 'obj'}
f1.call( obj )
```

- 题目9：
```js
function f1(){
    console.log(this) // 第一个 this
    function f2(){
        console.log(this) // 第二个 this
    }
    f2.call()
}
var obj = {name: 'obj'}
f1.call( obj )
为什么两个 this 值不一样？
```
本题答案：
=>每个函数都有自己的 this，为什么会一样？
=>this 就是 call 的第一个参数，第一个 this 对应的 call 是 f1.call(obj)，第二个 this 对应的 call 是 f2.call()
 =>this 和 arguments 都是参数，参数都要在函数执行（call）的时候才能确定


