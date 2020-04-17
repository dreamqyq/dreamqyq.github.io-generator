---
title: "JS里的数据类型转换"
date: 2018-06-10
draft: false
categories: ["JavaScript"] 
tags: ["JavaScript"]
---

**前言**：之前的博客介绍了JS中的各种数据类型，那么可不可以把已经确定的数据类型转换成其他的数据类型呢？本文就将介绍一些方法达成此目的，另外还会引申的简单介绍一下JS中的内存管理、深拷贝与浅拷贝等相关知识。

## 1、如何将其他的数据类型转换成String字符串类型？
注：下面介绍的三种转换成字符串的方法都不适用于对象，所得结果都是"[object Object]"
- `toSring()` 
toString方法适用于number类型和boolean类型，如：
```js
true.toString(); // "true"
 (11).toString(); // "11"
```
但是对于null和undefined，使用这种方法会报错，如：
```js
undefined.toString(); // Cannot read property 'toString' of undefined
null.toString(); // Cannot read property 'toString' of null
```
对于object，toSring方法结果不正确，结果永远是"[object Object]"
```js
var obj = {}
obj.toString()
==> 结果为 "[object Object]"
```

- `String()` 该方法适用于所有数据类型（除了对象，结果同`toString()`）
```js
String(obj); // "[object Object]"
String(11); // "11"
String(true); // "true"
String(undefined); // "undefined"
```
- `+ ''` 即使用+运算符加上空字符串（同样对object无效）
注：上面介绍的toString()以及String()方法是比较常规的方法，而这种不常规的操作，才是程序员的日常。这种方法的原理是：**‘+’ 运算符只能相加相同的数据类型，如果两边的数据类型不同，他会优先将其转换成字符串来相加**。因此就有一个很常见的坑：`1+'1' `的结果是多少？有些经验尚浅的程序员会以为结果是2，但很明显，这句话是按照字符串相加的规则来计算的，所以结果为`"11"`

使用此方法转换成字符串的例子如下：
```js
true+""; // "true"
undefined+""; // "undefined"
obj+""; // "[object Object]"
11+""; // "11"
```

## 2、如何将其他的数据类型转换成Number数值类型？

- `Number()`
使用Number函数，可以将任意类型的值转化成数值。Number函数将字符串转为数值，要比parseInt函数严格很多。**基本上，只要有一个字符无法转成数值，整个字符串就会被转为NaN。**
示例如下：
```js
// 数值：转换后还是原来的值
Number(324) // 324

// 字符串：如果可以被解析为数值，则转换为相应的数值
Number('324') // 324

// 字符串：如果不可以被解析为数值，返回 NaN
Number('324abc') // NaN

// 空字符串转为0
Number('') // 0

// 布尔值：true 转成 1，false 转成 0
Number(true) // 1
Number(false) // 0

// undefined：转成 NaN
Number(undefined) // NaN

// null：转成0
Number(null) // 0
```

- `parseInt()`
parseInt方法用于将字符串转为整数。
如：`parseInt('123') // 123`
如果字符串头部有空格，空格会被自动去除。`parseInt('   81') // 81`
如果parseInt的参数不是字符串，则会先转为字符串再转换。
```js
parseInt(1.23) // 1
// 等同于
parseInt('1.23') // 1
```
字符串转为整数的时候，是一个个字符依次转换，如果遇到不能转为数字的字符，就不再进行下去，返回已经转好的部分，如：
```js
parseInt('12.34') // 12
parseInt('15e2') // 15
parseInt('15px') // 15
```
如果字符串的第一个字符不能转化为数字（后面跟着数字的正负号除外），返回NaN。
```js
parseInt('abc') // NaN
parseInt('.3') // NaN
parseInt('') // NaN
parseInt('+') // NaN
parseInt('+1') // 1
```
注：parseInt()方法默认转换成十进制，不过需要注意的是，如果参数本身就是number类型，且是**0x开头（16进制），或0o开头（八进制），0b开头（二进制），0开头且后面的数字没有8和9（视为八进制）**，那么parseInt方法会将其以相应的进制转换成十进制展示出来。另外，**如果参数是0x开头的字符串**，也会以16进制解析，如`parseInt('0x10') // 16`
**因此，为了防止意外解析成其他进制，建议添加第二个参数按照特定进制解析：**如：
```js
parseInt('0x16'); // 22
parseInt('0x16',10) ; // 0
```


- `parseFloat()`
parseFloat方法用于将一个字符串转为浮点数。`parseFloat('3.14') // 3.14`
如果字符串符合科学计数法，则会进行相应的转换。
```js
parseFloat('314e-2') // 3.14
parseFloat('0.0314E+2') // 3.14
```
如果字符串包含不能转为浮点数的字符，则不再进行往后转换，返回已经转好的部分。如：`parseFloat('3.14more non-digit characters') // 3.14`
parseFloat方法会自动过滤字符串前导的空格。如：`parseFloat('\t\v\r12.34\n ') // 12.34`
如果参数不是字符串，或者字符串的第一个字符不能转化为浮点数，则返回NaN。
```js
parseFloat([]) // NaN
parseFloat('FF2') // NaN
parseFloat('') // NaN  注意，parseFloat会将空字符串转为NaN。
```
parseFloat的转换结果不同于Number函数的地方如下：
```js
parseFloat(true)  // NaN
Number(true) // 1

parseFloat(null) // NaN
Number(null) // 0

parseFloat('') // NaN
Number('') // 0

parseFloat('123.45#') // 123.45
Number('123.45#') // NaN
```

- 程序员爱用的非常规方法：字符串 - 0
如：` '22' - 0 `
- 更加非常规的方法： + 字符串，这里的+并不是取正值的意思，负数一样可行
如：` + '22' `、`+ '-011' ; // -11`

注意：上面介绍的使用运算符的转换方法，字符串不能有除了数字外的其他字符（正负号，表示进制的标识除外），字符串中，0b、0x、0o开头会以对应表示的进制解析，如果是'011'，则会解析成十进制而不是二进制，小数同样可以用这两种方法进行转换：
```js
'011'-0  // 11
'0b11'-0  // 3
'0o11'-0  // 9
'0x11'-0  // 17
'0x11.1'-0  // NaN
'011.1'-0  // 11.1
```


##3、如何将将其他数据类型转换为Boolean布尔类型？

- 常规方法：`Boolean()` ，如：
```js
Boolean("ss"); // true
Boolean({}); // true
```
- 程序员喜欢用的非常规方法 ，双重取反：`!! x` ，如：
```js
!!"ss" ; // true
!!NaN ; // false
!!{} ; // true
```
- 五个falsy值，即转换成Boolean后为false的值：
0 、 NaN 、 null 、 undefined 、‘’（空字符串）

## 4、关于JS中的数据在内存中的存储方式

- 内存
什么是内存呢，举个例子：你买一个8G 的内存条，操作系统开机即占用 512MB，Chrome 打开即占用 1G 内存， Chrome 各每个网页分配一定数量的内存，  这些内存要分给**页面渲染器**、**网络模块**、**浏览器外壳**和 **JS 引擎（V8引擎）**。
 JS 引擎将内存分为**代码区**和**数据区**，  我们只研究数据区。  数据区分为 **Stack（栈内存） **和 **Heap（堆内存）**。  简单类型的数据（如Number，string等）直接存在 Stack 里， 复杂类型的数据（object对象）是把 Heap 地址存在 Stack 里。如图：
![JS存储数据](https://upload-images.jianshu.io/upload_images/11827773-6ebdbdbcf98ff332.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 对象的存储方式
上一条说了，**对象的存储方式是在stack内存存储一个地址，形成对对象的引用，地址指向heap内存的某个位置**，这样才能达到可以随时为对象添加或删除内容的目的。所以有句话这么说，object是对象的引用。它在stack内存存的是地址，而不像其他数据类型直接把内容存在stack内存。

-  内存图
因此，这里就要引入“内存图”的概念，方便解决一些复杂问题，具体的内存图样式和应用会在第六部分的面试题中体现。

- 垃圾回收
JS中的垃圾回收机制：**如果一个对象没有被引用，他就是垃圾，将会被回收。**
**内存泄漏**就和垃圾回收机制有一定的联系：由于浏览器的一些bug，使得本应被被标记为垃圾的数据没有被标记，而这些垃圾数据占用的内存将永远被占用，哪怕你把当前页面关掉都不会被释放，除非直接关掉整个浏览器。（IE6就有此类bug）

## 5、深拷贝与浅拷贝
- **深拷贝**：
先举个例子：
```js
var a = 1;
var b = a;
b = 2 ;
```
那么经过了上述操作，很明显a的值仍然还是1。
即 **b 变而不影响 a**，就叫深拷贝（**简单数据类型的赋值都是深拷贝**）
- **浅拷贝**
同样举个栗子先：
```js
var a = {x:1} ;
var b = a ;
b.x = 2;
```
经过了上述操作，明显，a.x也变成了2.
即 **b 变而导致了a变**，这就是浅拷贝。

- 那么有人就问了，难道对象就不能实现深拷贝了吗？
当然……不是，对象的深拷贝的基本原理大概就是在`b = a`的这个环节，让 a所引用的对象复制一份，然后重新存在heap内存中的另一个位置，然后b再引用新生成的对象的地址，这样就实现了对象的深拷贝，不过具体代码很复杂，这里暂且不表。


## 6、几道面试题目

- 题目一：
```javascript
var a = 1
var b = a
b = 2
请问 a 显示是几？  
```
内存图解决思路：
![题目一](https://upload-images.jianshu.io/upload_images/11827773-fee2088b08367680.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 题目二：
```javascript
var a = {name: 'a'}
var b = a
b = {name: 'b'}
请问现在 a.name 是多少？
```
![题目二](https://upload-images.jianshu.io/upload_images/11827773-7f811e9c0ad9ee02.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 题目三：
```javascript
var a = {name: 'a'}
var b = a
b.name = 'b'
请问现在 a.name 是多少？
```
![题目三](https://upload-images.jianshu.io/upload_images/11827773-ca165faa27bffc56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 题目四：
```javascript
var a = {name: 'a'}
var b = a
b = null
请问现在 a 是什么？
```
![题目四](https://upload-images.jianshu.io/upload_images/11827773-5bb9a303b0b4d22f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 题目五：
```js
var a = {n:1};  
var b = a; 
a.x = a = {n:2};  
alert(a.x);
alert(b.x);
```
这道题最坑的一点在这句话`a.x = a = {n:2}; `，很显然如果在日常工作中这么写代码迟早得被打死，但是既然作为题目被写出来了，那咱们还是先仔细分析一下这句话：

赋值是从右到左的，但不要被绕晕了， 其实很简单，从运算符优先级来考虑
`a.x = a = {n:2};`
.运算优先于=赋值运算，因此此处赋值可理解为
>声明a对象中的x属性，用于赋值，此时b指向a，同时拥有未赋值的x属性
对a对象赋值，此时变量名a改变指向到对象{n:2}
对步骤1中x属性，也即a原指向对象的x属性，也即b指向对象的x属性赋值
赋值结果：
a => {n: 2}
b => {n: 1, x: {n: 2 } }    

然后老办法，我们画个内存图分析一下吧：
![题目五](https://upload-images.jianshu.io/upload_images/11827773-f69e3ab5fd8fd6b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 7、JS中对象的循环引用
什么是对象的循环引用呢？举个例子先：
```js
var obj = {name:'enoch'} ; 
obj.self = obj; 
obj.self.self.self.name; // "enoch"
```
上述代码就可以实现对象的循环引用，原理是：为对象添加一个属性，属性值就是对象自己（在内存中就是对象引用的地址），从而达到可以通过引用该对象的self属性来引用对象本身。