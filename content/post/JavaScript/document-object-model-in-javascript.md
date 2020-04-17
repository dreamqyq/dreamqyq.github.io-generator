---
title: "JavaScript中的文档对象模型DOM"
date: 2018-06-18
draft: false
categories: ["JavaScript"] 
tags: ["JavaScript"]
---

**前言**：在JavaScript中，DOM操作虽然公认的不好用，但它是最基础的，那么就让我们通过本文就来认识一下DOM的基本概念、一些常用的API以及一些需要注意的知识点。

---

## 1、DOM 到底是个什么鬼

>文档对象模型 (Document Object Model ，即DOM) 是HTML和XML文档的编程接口。它提供了对文档的结构化的表述，并定义了一种方式可以使从程序中对该结构进行访问，从而改变文档的结构，样式和内容。DOM 将文档解析为一个由节点和对象（包含属性和方法的对象）组成的结构集合。简言之，它会将web页面和脚本或程序语言连接起来。

文档对象模型将 web 页面与到脚本或编程语言连接起来。通常是指  JavaScript，但将 HTML、SVG 或 XML 文档建模为对象并不是 JavaScript 语言的一部分。DOM模型用一个逻辑树来表示一个文档，树的每个分支的终点都是一个节点(node)，每个节点都包含着对象(objects)。DOM的方法(methods)让你可以用特定方式操作这个树，用这些方法你可以改变文档的结构、样式或者内容。节点可以关联上事件处理器，一旦某一事件被触发了，那些事件处理器就会被执行。

**通俗的说，DOM就是将页面中的元素通过相应的构造方法，转换成对象，使得我们可以在JS中使用。转换成的对象的原型最终指向`Node`对象**。

## 2、节点

>DOM 的最小组成单位叫做节点（node）。文档的树形结构（DOM 树），就是由各种不同类型的节点组成。每个节点可以看作是文档树的一片叶子。

**节点的类型有以下七种**，浏览器提供一个原生的节点对象Node，而这七种节点都继承了Node，因此具有一些共同的属性和方法：
- Document：整个文档树的顶层节点
- DocumentType：doctype标签（比如<!DOCTYPE html>）
- Element：网页的各种HTML标签（比如`<body>`、`<a>`等）
- Attribute：网页元素的属性（比如class="right"）
- Text：标签之间或标签包含的文本
- Comment：注释
- DocumentFragment：文档的片段

## 3、节点树

>一个文档的所有节点，按照所在的层级，可以抽象成一种树状结构。这种树状结构就是 DOM 树。它有一个顶层节点，下一层都是顶层节点的子节点，然后子节点又有自己的子节点，就这样层层衍生出一个金字塔结构，倒过来就像一棵树。

**浏览器原生提供document节点，代表整个文档。**

文档的第一层只有一个节点，就是 HTML 网页的第一个标签`<html>`，它构成了树结构的根节点（root node），其他 HTML 标签节点都是它的下级节点。

除了根节点，其他节点都有三种层级关系。

- 父节点关系（parentNode）：直接的那个上级节点
- 子节点关系（childNodes）：直接的下级节点
- 同级节点关系（sibling）：拥有同一个父节点的节点

DOM 提供操作接口，用来获取这三种关系的节点。比如，子节点接口包括`firstChild`（第一个子节点）和`lastChild`（最后一个子节点）等属性，同级节点接口包括`nextSibling`（紧邻在后的那个同级节点）和`previousSibling`（紧邻在前的那个同级节点）属性。

## 4、节点类型 Node.nodeType

`nodeType`属性返回一个整数值，表示节点的类型。

`document.nodeType // 9`
上面代码中，文档节点的类型值为9。

Node 对象定义了几个常量，对应这些类型值。如：
`document.nodeType === Node.DOCUMENT_NODE // true`
上面代码中，文档节点的nodeType属性等于常量Node.DOCUMENT_NODE。

不同节点的nodeType属性值和对应的常量如下。

> 文档节点（document）：**9**，对应常量`Node.DOCUMENT_NODE` <br>
> 元素节点（element）：**1**，对应常量`Node.ELEMENT_NODE` <br>
> 属性节点（attr）：**2**，对应常量`Node.ATTRIBUTE_NODE` <br>
> 文本节点（text）：**3**，对应常量`Node.TEXT_NODE` <br>
> 文档片断节点（DocumentFragment）：**11**，对应常量`Node.DOCUMENT_FRAGMENT_NODE` <br>
> 文档类型节点（DocumentType）：**10**，对应常量`Node.DOCUMENT_TYPE_NODE` <br>
> 注释节点（Comment）：**8**，对应常量`Node.COMMENT_NODE` <br>


**最常用的有两个，一定要记住：元素节点的值 1 和文本节点的值 3** 。

确定节点类型时，使用`nodeType`属性是常用方法。如：
```js
var node = document.documentElement.firstChild;
if (node.nodeType === Node.ELEMENT_NODE) { // 或者直接写成 node.nodeType === 1
  console.log('该节点是元素节点');
}
```

## 5、常用的DOM操作

- `createElement`
createElement通过传入指定的一个标签名来创建一个元素，如果传入的标签名是一个未知的，则会创建一个自定义的标签，注意：IE8以下浏览器不支持自定义标签。
如：`var div = document.createElement("div"); `

- `appendChild`
appendChild 即将指定的节点添加到调用该方法的节点的子元素的末尾。调用方法如下：`parent.appendChild(child); `
child节点将会作为parent节点的最后一个子节点。

- `document.getElementById`
这个接口很简单，根据元素id返回元素，返回值是Element类型，如果不存在该元素，则返回null。
使用这个接口有几点要注意：
（1）元素的Id是大小写敏感的，一定要写对元素的id
（2）HTML文档中可能存在多个id相同的元素，则返回第一个元素(当然多个同名id是不符合规范的)
（3）只从文档中进行搜索元素，如果创建了一个元素并指定id，但并没有添加到文档中，则这个元素是不会被查找到的

- `document.getElementsByTagName`
这个接口根据元素标签名获取元素，返回一个即时的HTMLCollection类型

- `document.getElementsByClassName ` 
这个API是根据元素的class返回一个即时的HTMLCollection，

- `document.querySelector` 和 `document.querySelectorAll`
这两个api很相似，通过css选择器来查找元素，注意选择器要符合[CSS选择器](https://developer.mozilla.org/en-US/docs/Web/Guide/CSS/Getting_Started/Selectors)的规则。

- `childNodes`：返回一个即时的NodeList，表示元素的子节点列表，子节点可能会包含文本节点，注释节点等。
children：一个即时的HTMLCollection，子节点都是Element，IE9以下浏览器不支持。

- `getAttribute`
getAttribute返回指定的特性名相应的特性值，如果不存在，则返回null或空字符串。用法如下：
` var value = element.getAttribute("id"); `

-  `getBoundingClientRect `
getBoundingClientRect用来返回元素的大小以及相对于浏览器可视窗口的位置，用法如下：`var clientRect = element.getBoundingClientRect(); `
clientRect是一个DOMRect对象，包含left，top，right，bottom，它是相对于可视窗口的距离，滚动位置发生改变时，它们的值是会发生变化的。除了IE9以下浏览器，还包含元素的height和width等数据，具体可查看[MDN链接](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect) 。


## 6、DOM 的各种 API

由于DOM中的API非常多，这里不一一列举，仅列出几个需要注意的，其余的可以点击链接，查看相应的MDN文档。

- [Node 接口](https://developer.mozilla.org/zh-CN/docs/Web/API/Node)
- [Document 接口](https://developer.mozilla.org/zh-TW/docs/Web/API/Document)
- [Element 接口](https://developer.mozilla.org/zh-CN/docs/Web/API/Element)

这三对API的区别需要了解一下：
- `isEqualNode` 和 `isSameNode`
`isEqualNode` 表示相等，即两个节点的值是相等的
`isSameNode` 表示相同，即两个节点就是同一个
注意，节点中使用`===`运算符，指的是same相同的一个节点。

- `innerText` 和 `textContent`
1.`textContent` 会获取所有元素的内容，包括` <script>` 和 `<style>` 元素，然而 `innerText `不会。
2.`innerText` 受 CSS 样式的影响，并且不会返回隐藏元素的文本，而`textContent`会。
3.由于 `innerText` 受 CSS 样式的影响，它会触发重排（reflow），但`textContent` 不会。
4.`与 textContent 不同的是`, 在 Internet Explorer (对于小于等于 IE11 的版本) 中对 innerText 进行修改， 不仅会移除当前元素的子节点，而且还会永久性地破坏所有后代文本节点（所以不可能再次将节点再次插入到任何其他元素或同一元素中）。


- `innerText` 和 `innerHTML`
`innerText` 表示 将里面的内容都一律看做文本
`innerHTML` 会将内容看做HTML元素（包括标签甚至script脚本），慎用

## 7、一些需要注意的知识点
- DOM的最小组成单位叫做 `Node`
- 节点的类型有七种 ：`Document`、`DocumentType` 、`Element`、`Attribute`、`Text`、`Comment`、`DocumentFragment`（详细请看上文“4、节点类型 Node.nodeType”）
- DOM 树的根节点是 `html`
- `document.body.nodeName `得到的结果是`'BODY'`，（注意，nodeName得到的节点名称都是大写，除了svg）
- 有这样的html结构，求`x.nextSibling` 的值是
```html
<div id=x></div>
<div id=y></div>
```
结果为：回车构成的文本节点，因为`nextSibling`属性包括文本节点(如回车、空格)、注释节点等。
- 有这样的HTML结构，求`parent1.childNodes` 的值
```html
<div id=parent1><div id=child1></div></div>
```
结果为： {0:child1, length:1} 伪数组
-  `parent.childNodes` 是动态集合。所谓动态集合就是一个活的集合，DOM树删除或新增一个相关节点，都会立刻反映在NodeList接口之中。
 `document.querySelectorAll`方法返回的是一个静态集合。DOM内部的变化，并不会实时反映在该方法的返回结果之中。
- HTMLCollection与NodeList的区别有：
 `HTMLCollection`实例对象的成员只能是Element节点，NodeList实例对象的成员可以包含其他节点。
`HTMLCollection`实例对象可以用id属性或name属性引用节点元素，NodeList只能使用数字索引引用。
- ChildNode接口用于处理子节点（包含但不限于Element子节点）。Element节点、DocumentType节点和CharacterData接口，部署了ChildNode接口。凡是这三类节点（接口），都可以使用：`remove()`、` before()`、` after()`、` replaceWith()`


## 8、参考链接

- 相关博客：http://luopq.com/2015/11/30/javascript-dom/
- 阮一峰JS教程：http://javascript.ruanyifeng.com/dom/node.html
- MDN：https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model/Introduction
