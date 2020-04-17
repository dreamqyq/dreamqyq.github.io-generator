---
title: "从MVC到MVVM（初识Vue）"
date: 2018-08-28
draft: false
categories: ["前端框架"] 
tags: ["Vue"]
---

**前言**：看本文之前需要了解最基本的MVC思想（附一篇本人之前写的[MVC设计模式在JavaScript中的运用](https://www.jianshu.com/p/b3b2ce04c23d) 仅供参考）。在本篇文章中，我将先用原生JavaScript做一个小例子，然后将其先使用**MVC**设计模式进行代码重构，然后使用**Vue**框架再改写一遍，最终的代码就是使用的**MVVM**设计模式，从而让我们更容易的理解Vue的思路，从而在学习和工作中更好的使用Vue。

注：本文后面的MVC和Vue重构后的最终代码都会贴上JSBin的链接。在本地测试可能有bug，可使用JSBin在线测试。

## 1、先了解本文demo的基本需求，然后打个样

我们要做一个书籍列表，包括书本的名字和数量，
有三个按钮，可以增加或减少或清零书本的数量。
大致效果就像：

![](https://upload-images.jianshu.io/upload_images/11827773-169a07933ddf8f7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从需求就能看出其实原理非常简单，所以我们先写一个最初的静态无数据库的版本：
HTML结构如下：
```html
<section class="booksList">
  <p class="booksContent">
    书名：<span class="bookName">《JavaScript高级程序设计》</span> 数量：
    <span class="bookNum">__bookNum__</span>
  </p>
  <div class="buttons">
    <button id="addOne">ADD 1</button>
    <button id="reduceOne">REDUCE 1</button>
    <button id="reset">RESET</button>
  </div>
</section>
```
JavaScript代码：
```js
let content = document.querySelector('.booksContent').innerHTML
document.querySelector('.booksContent').innerHTML = content.replace('__bookNum__', 2)
let bookNum = parseInt(document.querySelector('.bookNum').innerText, 10)
document.querySelector('#addOne').onclick = function() {
  bookNum += 1
  document.querySelector('.booksContent').innerHTML = content.replace('__bookNum__', bookNum)
}
document.querySelector('#reduceOne').onclick = function() {
  bookNum -= 1
  document.querySelector('.booksContent').innerHTML = content.replace('__bookNum__', bookNum)
}
document.querySelector('#reset').onclick = function() {
  bookNum = 0
  document.querySelector('.booksContent').innerHTML = content.replace('__bookNum__', bookNum)
}
```
上面的代码能实现最起初的需求，但是这还不够！这种写法我们其实修改的是HTML中的数据，无论怎么点击按钮，网页一刷新，书的数量还是默认的2，这就很无趣。

在实际开发中，我们的书名以及书的数量应该从后台的数据库中读取，所以我们应该有ajax请求。所以请看下一步：

## 2、认识一个新朋友：axios

既然需要后台，就得有AJAX，那么就得有jQuery，但是今天我不准备使用jQuery，所以就要引入本节的标题：[axios](https://github.com/axios/axios) ，这是一个专门用来实现AJAX的库，它基于Promise的HTTP客户端，用于浏览器和node.js 。基本使用方法和jQuery类似，当然提供了更多的功能，比如`axios.post()`、`axios.get()`、`axios.put()`、`axios.patch()`、`axios.delete()`等，这个库除了AJAX功能外就没有其他的功能了，所以也可以说它更专注。

我们为什么要用这个库呢？主要还是为了后面的转到Vue时更容易理解，使用Vue这个框架时，一般都是使用axios来操作AJAX，Vue的作者也推荐使用。至此我们就可以完全抛弃jQuery了。

关于axios库本文不做深入介绍，可进入axios的[github主页](https://github.com/axios/axios)查看相关文档。

## 3、使用axios 假装做一个后台

为什么要说假装呢？因为我们确实没有服务器来做后台，而axios提供了一个可以劫持当前网页请求的api，即`axios.interceptors`：
- 相当于劫持了网页中发送的请求request，以及响应response，所以请求没有真的被后台的逻辑处理，而是axios通过`interceptors`这个api使用该方法下的逻辑自己处理这个请求后返回给页面一个假的响应。

所以，我们的代码就可以是这样的：
- HTML中只需引入axios库，然后让书名也从数据库中取得，所以将书名先写成`__bookName__`方便替换，书的数量还是`__bookNum__`：
```js
<!-- 引入axios库 -->
<script src="https://cdn.bootcss.com/axios/0.18.0/axios.min.js"></script>
<!-- HTML结构 -->
<section class="booksList">
  <p class="booksContent">
    书名：<span class="bookName">__bookName__</span> 数量：
    <span class="bookNum">__bookNum__</span>
  </p>
  <div class="buttons">
    <button id="addOne">ADD 1</button>
    <button id="reduceOne">REDUCE 1</button>
    <button id="reset">RESET</button>
  </div>
</section>
```
- 假装有后台的代码，将其封装成了一个函数`fakeData`：
```js
function fakeData() {
  // 一个假的数据库book
  let book = {
    name: 'JavaScript 高级程序设计',
    number: 2,
    id: 1
  }
  // 在真正返回response之前使用
  axios.interceptors.response.use(function(response) {
    // 获取请求的数据
    let {config: {method, url, data}} = response
    if (url === '/books/1' && method === 'get') {
      response.data = book
    } else if (url === '/books/1' && method === 'put') {
      data = JSON.parse(data)
      // 如果是PUT请求，说明要改后台数据，因此将数据库book中的数据部分更新即可
     
      Object.assign(book, data)
      response.data = book
      console.log(book) //将数据库打印出来 
    }
    return response
  })
}
```

- JavaScript使用后台请求的版本：
```js
fakeData()
axios.get('/books/1').then(({data})=>{
  let originalContent = document.querySelector('.booksContent').innerHTML
  let newContent = originalContent.replace('__bookName__',data.name).replace('__bookNum__',data.number)
  document.querySelector('.booksContent').innerHTML = newContent
})

document.querySelector('#addOne').onclick = function() {
  let bookNum = parseInt(document.querySelector('.bookNum').innerText, 10)
  bookNum += 1
  axios.put('/books/1',{num:bookNum}).then(()=>{
    document.querySelector('.bookNum').innerHTML = bookNum
  })
}
document.querySelector('#reduceOne').onclick = function() {
  let bookNum = parseInt(document.querySelector('.bookNum').innerText, 10)
  bookNum -= 1
   axios.put('/books/1',{num:bookNum}).then(()=>{
    document.querySelector('.bookNum').innerHTML = bookNum
  })
}
document.querySelector('#reset').onclick = function() {
  let bookNum = parseInt(document.querySelector('.bookNum').innerText, 10)
  bookNum = 0
   axios.put('/books/1',{num:bookNum}).then(()=>{
    document.querySelector('.bookNum').innerHTML = bookNum
  })
}
```

然后我们会发现，即使是这么小的需求，居然都写成了看起来如此混乱的代码，有大量重复的逻辑，业界给这种代码取名为**意大利面条式代码**，这种代码非常难以维护，简直人神共愤，因此为了小伙伴们的人身安全，我们还是使用MVC的思想来改写一下吧。


## 4、使用MVC设计模式改写代码

我们知道MVC设计模式分为三部分：
- `Model`层负责数据管理，包括数据逻辑、数据请求、数据存储等功能。前端 Model 主要负责 AJAX 请求或者 LocalStorage 存储
- `View`是表现层，负责用户界面，前端 View 主要负责 HTML 渲染。
- `Controller` 层负责处理View 的事件，并更新 Model；也负责监听 Model的变化，并更新 View，Controller 控制其他的所有流程。

因此，我们将上面的JavaScript代码进行如下改写：
- HTML 中我们可以让页面中的元素由 JS 填充，所以直接改写成：
```html
<!-- 需要获取的DOM元素太多，元素JS比较麻烦，直接引入jQuery -->
<script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
<!--body中只需要这一行即可，内容由JavaScript来填充-->
<section id="app"></section>
```
- Vew 层代码：
封装一个 View 类，使得我们可以多次使用 ：
```js
function View({el, template}){
  this.el = el
  this.template = template
}
View.prototype.render = function(data){
  let html = this.template
  for(let key in data){
    html = html.replace(`__${key}__`, data[key])
  }
  $(this.el).html(html)
}
```
使用 View 类：
```js
let view = new View({
  el: '#app',
  template: `
    <div>
    书名：《__name__》
    数量：<span id=number>__number__</span>
    </div>
    <div>
      <button id="addOne">加1</button>
      <button id="reduceOne">减1</button>
      <button id="reset">归零</button>
    </div>
  `
})
```
- Model 层代码：
先封装一个 Model 类：
```js
function Model(options){
  this.data = options.data
  this.resource = options.resource
}
Model.prototype.fetch = function(id){
  return axios.get(`/${this.resource}s/${id}`).then((response) => {
      this.data = response.data
      return response
    })
}
Model.prototype.update = function(data){
  let id = this.data.id
    return axios.put(`/${this.resource}s/${id}`, data).then((response) => {
      this.data = response.data 
      return response
    })
}
```
使用这个 Model 类：
```js
let model = new Model({
  data: {
    name: '',
    number: 0,
    id: ''
  },
  resource: 'book'
})
```
- Controller 层封装比较麻烦，本文就不将其封装成类了，直接声明：
```js
let controller = {
  init({view,model}){
    this.view = view
    this.model = model
    this.view.render(this.model.data)
    this.bindEvents()
    this.model.fetch(1).then(() => {
      this.view.render(this.model.data)
    })
  },
  addOne() {
    var oldNumber = $('#number').text() // string
    var newNumber = oldNumber - 0 + 1
    this.model.update({
      number: newNumber
    }).then(() => {
      this.view.render(this.model.data)
    })

  },
  reduceOne() {
    var oldNumber = $('#number').text() // string
    var newNumber = oldNumber - 0 - 1
    this.model.update({
      number: newNumber
    }).then(() => {
      this.view.render(this.model.data)
    })
  },
  reset() {
    this.model.update({
      number: 0
    }).then(() => {
      this.view.render(this.model.data)
    })
  },
  bindEvents() {
    // this === controller
    $(this.view.el).on('click', '#addOne', this.addOne.bind(this))
    $(this.view.el).on('click', '#reduceOne', this.reduceOne.bind(this))
    $(this.view.el).on('click', '#reset', this.reset.bind(this))
  }
}
```
- 最后直接调用各个函数即可：
```js
// 调用虚拟后台函数
fakeData()
// 调用 controller 中的 init 函数
controller.init({view:view, model: model})
```
本节完整代码：https://jsbin.com/zetuni/edit?html,js,output

## 5、直接将MVC模式的代码用 Vue 改写一下吧

首先我们要了解一点，Vue 框架，所代替的就是上文中 MVC 设计模式中的 **View** ，所以……话不多说，我们直接改代码，步骤如下：

1. 首先我们先引入Vue 。
`<script src="https://cdn.jsdelivr.net/npm/vue@2.5.17/dist/vue.js"></script>`
2. 将我们之前写的 View 类删掉，直接将其替换成 Vue ，即 `view = new Vue()`
3. Vue 中 template的标记和我们的不一样，Vue使用的是`{{xx}}`，xx是会替换的元素。
0. Vue 中的 `template` 只能有一个根元素，如果 `template` 有两个根元素， Vue 只会看第一个。
0. Vue 要求要将 `data` 放在view 层，而不是 Model 层 ，因为 Vue 需要根据 `data` 来初始化 `template` 。
0. 上一节的代码，我们的 view 层需要有一个 `render` 方法来渲染页面，使用 Vue 就不需要啦，Vue 有自动渲染的机制 。即我们直接改 view 层的 `data` ，html 会自动变更。
0. 而Vue 的 `data` 中的属性怎么改呢？Vue 会把 `data` 里面的所有属性升级到当前的 view 层上。所以我们修改的是 view 层上的 属性，比如：`number` 属性在我们之前的代码中，本应在 `this.view.data.number` （×）上，但实际上我们应该这么改 `this.view.number = xxx` （√）。
0. 需要注意的是：Vue 去替换或更新 html 中元素时，它不是一下全部更新，而是是更新局部需要变化的地方。比如之前的代码，数据一有变动，id 为`app` 里面的元素会全部重新渲染一遍，包括本来不需要变动的地方如按钮；而 Vue 更新数据，只会更新 id 为 `number` 的 `span`，其他的地方就不会再次渲染了。
0. Vue 甚至可以让你不需要 controller 层。所以我们可以把原先 controller 层中的所有相关操作都放在 view 层的 `methods` 上。而且我们不需要 `bindEvents` ，因为 Vue 内置，然后直接在 `template` 中的按钮上绑定对应的事件即可。
0. 那么没有了 controller 层，我们就需要在  view 层中进行第一步的获取数据然后初始化页面。我们可以在 view 层的 `created` 属性（为一个函数）中调用。
0. 我们仿佛一直在做赋值和取值这两件事情。Vue 这个框架就是让原来 MVC 中的 view 层更智能，然后 controller 层就可以合并到 view 层中 。
0. 更高端的改法：可以用户输入数据来操作加几，所以在 `template` 中添加一个`input`，将输入框的`value`数据`n`绑定，其中`n`在 `data` 中存储。
0. Vue 的双向绑定：上一步中添加了`input`，而我们一旦改了 `input` 中的值，Vue 就会发现 `input` 中的 `value` 的值变了，然后它就会去改 view 层中的 `data` 中的 `n` ，然后发现 `n` 变了后，页面中使用了 `n` 的地方的值即可就会改变，这就是Vue 的双向绑定。
0. Vue 也就是自动化的 MVC，所以也被称为 MVVM 。

改完后的完整代码（https://jsbin.com/dapotuj/edit?js,output）
其中把JS部分中的view 层代码贴到下面（model 层不变，controller 层可以接删掉）：
```js
let view = new Vue({
  el: '#app',
  data: {
    book: {
      name:'未命名',
      number: 0,
      id: ''
    },
    n:1
  },
  template: `
    <div>
      <div>
      书名：《{{book.name}}》
      数量：<span id=number>{{book.number}}</span>
      </div>
      <div>
        <input v-model="n" />
        <span>N的值为：{{n}}</span>
      </div>
      <div>
        <button v-on:click="addOne">加N</button>
        <button v-on:click="reduceOne">减N</button>
        <button v-on:click="reset">归零</button>
      </div>
    </div>
  `,
  created(){
    model.fetch(1).then(()=>{
      this.book = model.data
    })
  },
  methods: {
    addOne() {
      model.update({
        number: this.book.number + parseInt(this.n,10)
      }).then(() => {
        this.view.book = this.model.data
      })
    },
    reduceOne() {
      model.update({
        number: this.book.number - parseInt(this.n,10)
      }).then(() => {
        this.view.book = this.model.data
      })
    },
    reset() {
      model.update({
        number: 0
      }).then(() => {
        this.view.book = this.model.data
      })
    }
  }
})
```

## 6、出现了，传说中的 MVVM

上一节使用 Vue 改写后的代码，其实就是将传统的MVC变成使用 Vue 的MVC，也就是传说中的 MVVM，下面就来简单的介绍一下 MVVM 设计模式。

MVVM 模式有四个组成部分，分别是：
- **模型**
是指代表真实状态内容的领域模型（面向对象），或指代表内容的数据访问层（以数据为中心）。

- **视图**
就像在MVC模式中一样，视图是用户在屏幕上看到的结构、布局和外观（UI）。

- **视图模型**
*视图模型*是暴露公共属性和命令的视图的抽象。MVVM没有MVC模式的控制器，也没有MVP模式的presenter，有的是一个*绑定器*。在视图模型中，绑定器在视图和数据绑定器之间进行通信。

- **绑定器**
声明性数据和命令绑定隐含在MVVM模式中。在Microsoft解决方案堆中，绑定器是一种名为XAML。绑定器使开发人员免于被迫编写样板式逻辑来同步视图模型和视图。在微软的堆之外实现时，声明性数据绑定技术的出现是实现该模式的一个关键因素。

选取阮一峰关于MVVM的示意图让大家更直观的认识它：

![图来源于阮一峰博客](https://upload-images.jianshu.io/upload_images/11827773-027061ffcee3a359.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240))

关于MVVM设计模式本文就不深入介绍了，想要深入了解的可以点击下面的几个链接：
1. [维基百科](https://zh.wikipedia.org/wiki/MVVM)
2. [阮一峰 - MVC，MVP 和 MVVM 的图示](http://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)

（END）
