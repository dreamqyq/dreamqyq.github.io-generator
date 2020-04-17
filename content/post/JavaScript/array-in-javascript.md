---
title: "JavaScript里的数组详解"
date: 2018-07-09
draft: false
categories: ["JavaScript"] 
tags: ["JavaScript"]
---

**前言**：这篇文章用于了解总结JS里的**数组**的相关知识。我会从基本概念，构造方法和常用的API这几个方面来介绍数组。部分概念与代码摘自[阮一峰JS教程](http://javascript.ruanyifeng.com/grammar/array.html)。

## 1、数组是什么

>数组（array）是**按次序排列的一组值**。每个值的位置都有编号（从0开始），整个数组用方括号表示。

其实本质上，**数组属于一种特殊的对象**。typeof运算符会返回数组的类型是object。
数组的特殊性体现在，它的键名是按次序排列的一组整数（0，1，2…）。而由于数组成员的键名是固定的（默认总是0、1、2…），因此数组不用为每个元素指定键名，而对象的每个成员都必须指定键名。

## 2、数组的length属性

数组的length属性，返回数组的成员数量。**只要是数组，就一定有length属性**。该属性是一个动态的值，等于键名中的最大整数加上1。

## 3、数组的构造方法

- ` var x = new Array(3) ; // 3表示数组的长度，每一项都为undefined `
- ` var y = new Array(3,3); // 此时得到一个数组[3,3] ,即里面的两个参数表示数组的第0项和第1项 `

上面两行代码展示了JS的不一致行，即不同的参数个数，导致相同位置的参数所表示的意义是不同的。
使用`new Array()`的构造方法，是仿自Java中的写法，不常用。

因此，其实众所周知的函数构造方法是直接定义，如：
` var a = [3,3] ; // 得到了一个数组a，其中第0项和第一项分别为3、3`

## 4、in 运算符 && for…in 循环

检查某个键名是否存在的运算符in，适用于对象，也适用于数组。
因此，数组也是可以使用for in循环的，for...in循环不仅可以遍历对象，也可以遍历数组，毕竟数组只是一种特殊对象。
如：
```js
var a = [1, 2, 3];

for (var i in a) {
  console.log(a[i]);
}
// 1
// 2
// 3
```

但是，for...in不仅会遍历数组所有的数字键，还会遍历非数字键。**因此不建议使用。**
```js
var a = [1, 2, 3];
a.foo = true;

for (var key in a) {
  console.log(key);
}
// 0
// 1
// 2
// foo
```

## 5、数组的遍历

上面讲了使用for…in来遍历数组，但是不建议使用，我们常用的数组遍历方法有**for循环或while循环**。

如：
```js
var a = [1, 2, 3];

// for循环
for(var i = 0; i < a.length; i++) {
  console.log(a[i]);
}

// while循环
var i = 0;
while (i < a.length) {
  console.log(a[i]);
  i++;
}

var l = a.length;
while (l--) {
  console.log(a[l]);
}
```

## 6、数组的空位

当数组的某个位置是空元素，即两个逗号之间没有任何值，我们称该数组存在空位（hole），下面代码表明，数组的空位不影响length属性。
如：`var a = [1, , 1];   a.length // 3`
数组的空位是可以读取的，返回undefined。
使用delete命令删除一个数组成员，会形成空位，并且不会影响length属性。如：
```js
var a = [1, 2, 3];
delete a[1];

a[1] // undefined
a.length // 3
```
**注：数组的某个位置是空位，与某个位置是undefined，是不一样的。如果是空位，使用数组的forEach方法、for...in结构、以及Object.keys方法进行遍历，空位都会被跳过，如果某个位置是undefined，遍历的时候就不会被跳过。**
如：
```js
// 数组中有空位时
var a = [, , ,];

a.forEach(function (x, i) {
  console.log(i + '. ' + x);
})
// 不产生任何输出

for (var i in a) {
  console.log(i);
}
// 不产生任何输出

Object.keys(a)
// []

//数组中的值为undefined时
var a = [undefined, undefined, undefined];

a.forEach(function (x, i) {
  console.log(i + '. ' + x);
});
// 0. undefined
// 1. undefined
// 2. undefined

for (var i in a) {
  console.log(i);
}
// 0
// 1
// 2

Object.keys(a)
// ['0', '1', '2']
```
这就是说，空位就是数组没有这个元素，所以不会被遍历到，而undefined则表示数组有这个元素，值是undefined，所以遍历不会跳过。

## 7、类数组对象
如果一个对象的所有键名都是正整数或零，并且有length属性，那么这个对象就很像数组，语法上称为“类似数组的对象”（array-like object）。
典型的“类似数组的对象”是函数的`arguments`对象，以及大多数 DOM 元素集，还有字符串。

**数组与类数组对象最本质的区别为：类数组对象的原型链中没有`Array.prototype`，即：
`数组.__proto__ === Array.protype`
`类数组对象.__proto__ === Object.prototype`**

因此产生了一句很神奇的话：数组之所以是数组不是因为它本身是数组，而是因为你认为它是数组所以它才是数组。

## 8、数组的各种API

- `forEach()` 遍历
不支持`break` 和 `continue`
代码示例：
```js
array = ['a','b','c','d'] ;
array.forEach(function(value,key){
  console.log(key+':'+value);
})
// 0:a
// 1:b
// 2:c
// 3:d
```
其中：使用`forEach`循环遍历，原数组不会被改变，没有返回值

- `sort()` 排序
该方法是JS封装的一个重排序方法，使用的算法是“快速排序”。不传参时默认为升序排列。**注：`sort()`方法返回的是原数组，即使用该方法会改变原来的数组序列**。
=>`arr.sort(function(a,b){return a-b})` 升序
=>`arr.sort(function(a,b){return b-a})` 降序
上面两行代码中，为什么`return a-b`会升序排列，`return b-a`会降序排列呢？
原理如下：
>函数返回值 小于等于 -1 时,a在前，b在后
函数返回值 在区间（-1,1）时，序列不变
函数的返回值 大于等于 1 时 ，b在前，a在后

- `join()` 分隔
该方法返回的是字符串，参数表示用什么分隔数组的每一项，不传参默认逗号分隔，参数为空数组时，数组每一项之间没有间隔。
代码示例：
```js
array = ["a", "b", "c", "d"];
array.join('-');
// "a-b-c-d"
```
- `concat()` 连接
该方法返回的是新数组，原数组不会被改变。
代码示例：
 ```js
a = [1,2,30] ;
b = [42,22,33];
a.concat(b);
// 得到新数组 [1, 2, 30, 42, 22, 33]
```
使用`concat()`方法可以用于复制数组，如：
```js
arr = [1,2,3,4,5,6];
var newArr = arr.concat([]);
// newArr 的值为 [1, 2, 3, 4, 5, 6]
newArr === arr; // false
```
注：关于`newArr === arr`返回的是false，因为数组也是对象，所以参照对象的相等可得，newArr 和 arr 是两个不同地址的引用，只是恰好他们引用的地址中的值是一样的而已，而两个对象本身的值肯定是不同的。

- `map()` 
功能与forEach相同，但`map()`有返回值。同样不会改变原数组的值，返回的一个新数组。
代码示例：
```js
arr = [1, 2, 3, 4, 5, 6];
arr.map(function(value,key){
	return value * 2;
})
// [2, 4, 6, 8, 10, 12]
```

- `filter()` 过滤、筛选
用法与`map() `一样，代码示例：
```js
arr = [1, 2, 3, 4, 5, 6];
arr.filter(function(value,key){
	return value > 3
})
// [4, 5, 6]
```
- `reduce()` 可接收一个函数作为累加器
代码示例：计算数组中每一项相加的值
```js
arr = [1, 2, 3, 4, 5, 6];
arr.reduce(function(previous,current){
	return previous + current;
},0)
// 21
```
即`reduce()`方法，第一个参数传一个函数作为累加器，第二个参数表示初始值。

可以用`reduce()`方法来写`map()`数组每一项×2：
```js
a = [1, 2, 3, 4, 5, 6];
a.reduce(function(array,n){
	array.push(n * 2);
	return array;
},[])
// [2, 4, 6, 8, 10, 12]
```
可以用`reduce()`方法来写`filter()`筛选偶数：
```js
a = [1, 2, 3, 4, 5, 6];
a.reduce(function(array,n){
	if(n % 2 === 0){
            array.push(n);
        }
	return array;
},[])
// [2, 4, 6]
```





