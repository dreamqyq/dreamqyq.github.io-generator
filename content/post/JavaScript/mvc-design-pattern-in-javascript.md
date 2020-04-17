---
title: "MVC设计模式在JavaScript中的运用"
date: 2018-08-03
draft: false
categories: ["JavaScript"] 
tags: ["JavaScript"]
---

**前言**：MVC模式是一种很有名的、应用很广的设计模式，包括在前端领域也是非常有用的代码优化思路，本文就详细介绍总结一下MVC模式以及它JavaScript中的应用。（部分文字摘自[维基百科](https://zh.wikipedia.org/zh-hans/MVC)和[阮一峰博客](http://www.ruanyifeng.com/blog/2007/11/mvc.html)）

---

## 1、MVC 模式的概念
>**MVC模式**（Model–view–controller）是软件工程中的一种软件架构模式，把软件系统分为三个基本部分：模型（Model）、视图（View）和控制器（Controller）。

**MVC模式**的目的是**实现一种动态的程序设计，使后续对程序的修改和扩展简化，并且使程序某一部分的重复利用成为可能**。除此之外，此模式通过对复杂度的简化，使程序结构更加直观。软件系统通过对自身基本部分分离的同时也赋予了各个基本部分应有的功能。专业人员可以通过自身的专长分组：
*   **控制器**（Controller）- 负责转发请求，对请求进行处理。
*   **视图**（View） - 界面设计人员进行图形界面设计。
*   **模型**（Model） - 程序员编写程序应有的功能（实现算法等等）、数据库专家进行数据管理和数据库设计(可以实现具体的功能)。

## 2、MVC 模式的优点

在最初的JSP查询语句(SQL query)这样的数据层代码和像HTML这样的表示层代码是混在一起。虽然有着经验比较丰富的开发者会将数据从表示层分离开来，但这样的良好设计通常并不是很容易做到的，实现它需要精心地计划和不断的尝试。MVC可以从根本上强制性地将它们分开。尽管构造MVC应用程序需要一些额外的工作，但是它带给我们的好处是毋庸置疑的。

首先，多个 View 能共享一个 Model 。如今，同一个Web应用程序会提供多种用户界面，例如用户希望既能够通过浏览器来收发电子邮件，还希望通过手机来访问电子邮箱，这就要求Web网站同时能提供Internet界面和WAP界面。在MVC设计模式中， Model 响应用户请求并返回响应数据，View 负责格式化数据并把它们呈现给用户，业务逻辑和表示层分离，同一个 Model 可以被不同的 View 重用，所以大大提高了代码的可重用性。

其次，Controller 是自包含（self-contained）指高独立内聚的对象，与 Model 和 View 保持相对独立，所以可以方便的改变应用程序的数据层和业务规则。一旦正确地实现了控制器，不管数据来自数据库还是LDAP服务器，View 都会正确地显示它们。由于MVC模式的三个模块相互独立，改变其中一个不会影响其他两个，所以依据这种设计思想能构造良好的少互扰性的构件。

此外，Controller 提高了应用程序的灵活性和可配置性。Controller 可以用来连接不同的 Model 和 View 去完成用户的需求，也可以构造应用程序提供强有力的手段。给定一些可重用的 Model 、 View 和Controller 可以根据用户的需求选择适当的 Model 进行处理，然后选择适当的的 View 将处理结果显示给用户。

## 3、MVC 三个模块的具体分工：

MVC模式在概念上强调 Model, View, Controller 的分离，各个模块也遵循着由 Controller 来处理消息，Model 掌管数据源，View 负责数据显示的职责分离原则，因此在实现上，MVC 模式的 Framework 通常会将 MVC 三个部分分离实现：

**Model** 负责数据访问，较现代的 Framework 都会建议使用独立的数据对象 (DTO, POCO, POJO 等) 来替代弱类型的集合对象。数据访问的代码会使用 Data Access 的代码或是 ORM-based Framework，也可以进一步使用 Repository Pattern 与 Unit of Works Pattern 来切割数据源的相依性。
**Controller** 负责处理消息，较高阶的 Framework 会有一个默认的实现来作为 Controller 的基础。
**View** 负责显示数据，这个部分多为前端应用，而 Controller 会有一个机制将处理的结果 (可能是 Model, 集合或是状态等) 交给 View，然后由 View 来决定怎么显示。

## 4、画图表示

![图像来自方方](https://upload-images.jianshu.io/upload_images/11827773-e57052a06919d24c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**具体解释**：Model 和服务器交互，Model 将得到的数据交给 Controller，Controller 把数据填入 View，并监听 View
用户操作 View，如点击按钮，Controller 就会接受到点击事件，Controller 这时会去调用 Model，Model 会与服务器交互，得到数据后返回给 Controller，Controller 得到数据就去更新 View

## 5、前端中的MVC

- MVC 是什么：
MVC 是一种设计模式（或者软件架构），把系统分为三层：Model数据、View视图和Controller控制器。
- MVC在前端分别负责什么
  - `Model` 数据管理，包括数据逻辑、数据请求、数据存储等功能。前端 `Model` 主要负责 `AJAX` 请求或者 `LocalStorage` 存储
  - `View` 负责用户界面，前端 View 主要负责 `HTML` 渲染。
  - `Controller` 负责处理`View` 的事件，并更新 `Model`；也负责监听 `Model`的变化，并更新 `View`，`Controller` 控制其他的所有流程。

- 用代码大概说明 MVC 三个对象分别有哪些重要属性和方法：
```js
var model = {
    data: null,
    init(){}
    fetch(){}
    save(){}
    update(){}
    delete(){}
}
view = {
    init() {}
    template: '<h1>hi</h1'>
}
controller = {
    view: null,
    model: null,
    init(view, model){
        this.view = view
        this.model = model
        this.bindEvents()
    }
    render(){
        this.view.querySelector('name').innerText = this.model.data.name
    },
    bindEvents(){}
}
```


## 6、具体的例子：

代码链接：https://github.com/dreamqyq/resume/blob/master/js/message.js
**注**：这一段是完全使用了MVC设计思想优化过的代码，包括了说明操作的部分是哪一块的**view**部分，操作数据的对象的**model**部分，对view部分进行各种逻辑操作的**controller**部分。这段代码实现的使用[leancloud](https://leancloud.cn/)提供的库写的留言板功能。


```js
let view = document.querySelector('section.message')

  let model = {
    init : function(){
      var APP_ID = 'mhV5mbomQXQnB50TUsfuGmCB-gzGzoHsz'
      var APP_KEY = 'w4usAIsSAERkgINFpAfrtEN2'
      AV.init({ appId: APP_ID, appKey: APP_KEY })
    },
    fetch : function(){
      var query = new AV.Query('Message')
      return query.find()
    },
    save : function(name,content){
      var Message = AV.Object.extend('Message');
      var message = new Message();
      return message.save({
          name : name,
          content : content 
      })
    }
  }

  let controller = {
    view : null,
    model : null,
    form : null,
    messageList : null,
    init : function(view,model){
      this.view = view 
      this.model = model
      this.form = this.view.querySelector('#messageForm')
      this.messageList = this.view.querySelector('#messageList')
      this.model.init()
      this.loadMessage()
      this.bindEvents()
    },
    bindEvents : function(){
      this.form.addEventListener('submit',function(e){
        e.preventDefault()
        this.saveMessage()
      }.bind(controller))
    },
    loadMessage : function(){
      this.model.fetch()
        .then(
          function (messages) {
            messages.forEach(function(items){
              let li = document.createElement('li')
              li.textContent = `${items.attributes.name} : ${items.attributes.content}`
              this.messageList.appendChild(li)
            })
          }.bind(controller))
    },
    saveMessage : function(){
      let messageForm = this.form
      let name = messageForm.querySelector('input[name=name]').value
      let content = messageForm.querySelector('input[name=content]').value
      this.model.save(name,content)
        .then(function(object) {
          let li = document.createElement('li')
          li.textContent = `${object.attributes.name} : ${object.attributes.content}`
          this.messageList.appendChild(li)
          messageForm.querySelector('input[name=content]').value = ''
      })
    }
  }
  controller.init(view,model)
```