---
title: "使用Vue + TypeScript + TSX 实现CNode社区"
date: 2020-05-12
draft: false
categories: ["前端框架"] 
tags: ["Vue", "TypeScript"]
---

**前言**： 众所周知，`Vue`很优秀，`TypeScript`也很优秀，但是`Vue` + `TypeScript`就会出现各种奇奇怪怪的问题。本文就将介绍我在「CNode 社区」这个项目开发的过程中遇到一些问题和解决办法。希望对你在`Vue`中使用`TypeScript`有所帮助。

---

## 项目源码及预览地址
- [线上预览地址](https://dreamqyq.github.io/cnode-community-preview/)
- [项目源码](https://github.com/dreamqyq/cnode-community) 欢迎star，欢迎pr。

![效果预览](https://upload-images.jianshu.io/upload_images/11827773-10bd86751ca9a81a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

---

## 项目简介
仿[CNode社区](https://cnodejs.org/)，使用`Vue` + `TypeScript` + `TSX` 等相关技术栈实现了[原社区](https://cnodejs.org/)的看帖、访问用户信息、查看回复列表、查看用户信息、博客列表页分页查看等功能。
后端接口调用的是CNode 官方提供的[api](https://cnodejs.org/api)。
本项目中的所有组件都使用了Vue的渲染函数`render` 以及 `TSX`。

### 项目安装及启动
```bash
yarn install
yarn serve
```

### 技术栈
- `Vue @2.6.11`
- `TypeScript`
- `TSX`
- `SCSS`

---
## Vue + TypeScript 和 Vue的常规写法有什么不同
### 起手式
1. 首先我们要把`<script>`标签的`lang`属性改为ts，即`<script lang="ts">` 
0. 要在Vue项目中引入 `vue-property-decorator`，后续很多操作都需要引用这个库里面的属性（包括`Vue`，`Component` 等）。

### `shims-tsx.d.ts`、`shims-vue.d.ts`的作用
如果用vue-cli 直接生成一个「Vue + TS」的项目，我们会发现在 src 目录下出现了这两个文件，那么它们的作用是什么呢？
- `shims-vue.d.ts`
  > shims-vue.d.ts 这个文件，主要用于 TypeScript 识别.vue 文件，Ts 默认并不支持导入 vue 文件，这个文件告诉 ts 导入.vue 文件都按VueConstructor<Vue>处理，因此导入 vue 文件必须写.vue 后缀，但是这样同样的也会造成，就算你写的导入的 .vue 文件的路径就算是错的，静态检测也不会检测到错误，如果你把鼠标放上面你会看到错误的路径就是指向这个文件，因为你定义了这个模块是所有 .vue 后缀的导入都会指向到这个文件，但是如果你的路径是对的，ts 能读出正确的 module。
- ``shims-tsx.d.ts``
  >shims-tsx.d.ts 文件，这个文件主要是方便你使用在 ts 中使用 jsx 语法的，如果不使用 jsx 语法，可以无视这个，但是强烈建议使用 jsx 语法，毕竟模板是没法获得静态类型提示的，当然，如果你境界高的话，直接用 vue render function。

### 基于class的组件
- TypeScript 版本
  ```vue
  <script lang="ts">
  import { Component, Vue } from 'vue-property-decorator'
  @Component
  export default class HelloWorld extends Vue {
  }
  </script>
  ```
- JavaScript 版本
  ```vue
  <script>
  export default {
    name: 'HelloWorld'
  }
  </script>
  ```
### 引入组件 import component
- TypeScript 版本
  ```vue
  <template>
    <div class="main">
      <project />
    </div>
  </template>
  <script lang="ts">
  import { Component, Vue } from 'vue-property-decorator'
  import Project from '@/components/Project.vue'
  @Component({
    components: {
      project
    }
  })
  export default class HelloWorld extends Vue {
  }
  </script>
  ```
- JavaScript 版本
  ```vue
  <template>
    <div class="main">
      <project />
    </div>
  </template>
  <script>
  import Project from '@/components/Project.vue'
  export default {
    name: 'HelloWorld',
    components: {
      project
    }
  })
  </script>
  ```

### Data 数据
- TypeScript 版本
  ```vue
  @Component
  export default class HelloWorld extends Vue {
    private msg: string = "welcome to my app"
    private list: Array<object> = [
      {
        name: 'Preetish',
        age: '26'
      },
      {
        name: 'John',
        age: '30'
      }
    ]
  }
  ```
- JavaScript 版本
  ```vue
  export default {
    data() {
      return {
        msg: "welcome to my app",
        list: [
          {
            name: 'Preetish',
            age: '26'
          },
          {
            name: 'John',
            age: '30'
          }
        ]
      }
  }
  ```
### Computed 计算属性
- TypeScript 版本
  ```vue
  export default class HelloWorld extends Vue {
    get fullName(): string {
      return this.first+ ' '+ this.last
    }
    set fullName(newValue: string) {
      let names = newValue.split(' ')
      this.first = names[0]
      this.last = names[names.length - 1]
    }
  }
  ```
- JavaScript 版本
  ```vue
  computed: {
    fullName: {
      // getter
      get: function () {
        return this.firstName + ' ' + this.lastName
      },
      // setter
      set: function (newValue) {
        var names = newValue.split(' ')
        this.firstName = names[0]
        this.lastName = names[names.length - 1]
      }
    }
  }
  ```
### Methods 方法
在TS里面写methods，就像写`class`中的方法一样，有一个可选的修饰符。
- TypeScript 版本
  ```vue
  export default class HelloWorld extends Vue {
    public clickMe(): void {
      console.log('clicked')
      console.log(this.addNum(4, 2))
    }
    public addNum(num1: number, num2: number): number {
      return num1 + num2
    }
  }
  ```
- JavaScript 版本
  ```vue
  export default {
    methods: {
      clickMe() {
        console.log('clicked')
        console.log(this.addNum(4, 2))
      }
      addNum(num1, num2) {
        return num1 + num2
      }
    }
  }
  ```
### 生命周期钩子
生命周期钩子的写法和上一条写`methods`是一样的。Vue组件具有八个生命周期挂钩，包括`created`，`mounted`等，并且每个挂钩使用相同的TypeScript语法。这些被声明为普通类方法。由于生命周期挂钩是自动调用的，因此它们既不带参数也不返回任何数据。因此，我们不需要访问修饰符，键入参数或返回类型。
- TypeScript 版本
  ```vue
  export default class HelloWorld extends Vue {
    mounted() {
      //do something
    }
    beforeUpdate() {
      // do something
    }
  }

  ```
- JavaScript 版本
  ```vue
  export default {
    mounted() {
      //do something
    }
    beforeUpdate() {
      // do something
    }
  }
  ```

### Props
我们可以在Vue的组件里面使用`@Prop`装饰器来替代 `props`，在Vue中，我们能给props提供额外的属性，比如`required`, `default`, `type`。如果用TypeScript，我们首先需要从`vue-property-decorator`引入`Prop`装饰器。我们甚至可以用TS提供的`readonly`来避免在代码中不小心修改了`props`。
（备注：TypeScript中的赋值断言。`!:` 表示一定存在， `?:`表示可能不存在。）
- TypeScript 版本
  ```vue
  import { Component, Prop, Vue } from 'vue-property-decorator'
  @Component
  export default class HelloWorld extends Vue {
    @Prop() readonly msg!: string
    @Prop({default: 'John doe'}) readonly name: string
    @Prop({required: true}) readonly age: number
    @Prop(String) readonly address: string
    @Prop({required: false, type: String, default: 'Developer'}) readonly job: string
  }
  ```
- JavaScript 版本
  ```vue
  export default {
    props: {
      msg,
      name: {
        default: 'John doe'
      },
      age: {
        required: true,
      },
      address: {
        type: String
      },
      job: {
        required: false,
        type: string,
        default: 'Developer'
      }
    }
  }
  ```
### Ref
在Vue中我们经常会使用`this.$refs.xxx` 来调用某个组件中的方法，但是在使用TS的时候，有所不同：
```vue
<Loading ref="loading" />

export default class Article extends Mixins(LoadingMixin) {
  $refs!: {
    loading: Loading;
  };
}
```
  在`$refs`里面声明之后，TS就可以识别到 ref 属性了，调用方式和JS一样：`this.$refs.loading.showLoading();`

### Watch
要想用`watch`侦听器的话，在TS中就要使用`@Watch`装饰器（同样从`vue-property-decorator`引入）。
- TypeScript 版本
  ```ts
  @Watch('name')
  nameChanged(newVal: string) {
    this.name = newVal
  }
  ```
  我们还可以给`watch`添加`immediate`和`deep`属性：
  ```ts
  @Watch('name')
  nameChanged(newVal: string) {
    this.name = newVal
  }
  ```
- JavaScript 版本
  ```javascript
  watch: {
    person: {
        handler: 'projectChanged',
        immediate: true,
        deep: true
      }
  }
  methods: {
    projectChanged(newVal, oldVal) {
      // do something
    }
  }
  ```
### Emit
这里同样要从`vue-property-decorator`引入装饰器`@Emit`
- TypeScript 版本
  ```ts
  @Emit()
  addToCount(n: number) {
    this.count += n
  }
  @Emit('resetData')
  resetCount() {
    this.count = 0
  }
  @Emit('getCount')
  getCount(){
    return this.count
  }
  ```
  在上面这个例子中，`addToCount`方法回自动转换成**kebab-case命名**，即**中划线命名**，这和Vue的 emit 工作方式十分类似。
  而`resetCount`方法则不会自动转换成中划线命名，因为我们给`@Emit`传入了一个参数`resetCount`作为方法名。
  `getCount`这个方法可以向父组件传递参数，就像在JS中写成`this.$emit("getCount", this.count)`一样。
 
- JavaScript 版本
  ```js
  <some-component add-to-count="someMethod" />
  <some-component reset-data="someMethod" />

  //Javascript Equivalent
   methods: {
      addToCount(n) {
        this.count += n
        this.$emit('add-to-count', n)
      },
      resetCount() {
        this.count = 0
        this.$emit('resetData')
      }
  }
  ```
### Mixin
想要在Vue+TypeScript中使用mixin，首先我们先创建一个mixin文件：
```ts
import { Component, Vue } from 'vue-property-decorator'
@Component
class ProjectMixin extends Vue {
  public projName: string = 'My project'
  public setProjectName(newVal: string): void {
    this.projName = newVal
  }
}
export default ProjectMixin
```
想要使用上面代码中的mixin，我们需要从`vue-property-decorator` 中引入 `Mixins` 以及 包含上述代码的mixins 文件，具体写法如下，主要不同就是组件不继承自`Vue`，而是继承自`Mixins`：
```vue
<template>
  <div class="project-detail">
    {{ projectDetail }}
  </div>
</template>
<script lang="ts">
import { Component, Vue, Mixins } from 'vue-property-decorator'
import ProjectMixin from '@/mixins/ProjectMixin'
@Component
export default class Project extends Mixins(ProjectMixin) {
  get projectDetail(): string {
    return this.projName + ' ' + 'Preetish HS'
  }
}
</script>
```

### Vuex
Vuex是大多数Vue.js应用程序中使用的官方状态管理库。最好将`store`分为 namespaced modules，即带命名空间的模块。我们将演示如何在TypeScript中编写Vuex。
- 首先，我们要安装两个流行的第三方库：
  ```bash
  npm install vuex-module-decorators -D
  npm install vuex-class -D
  ```
- 在 `store`文件夹下，创建一个`module`文件夹用来放置不同的模块文件。比如创建一个拥有用户状态的文件`user.ts`：
  ```ts
  // store/modules/user.ts
  import { VuexModule, Module, Mutation, Action } from 'vuex-module-decorators'
  @Module({ namespaced: true, name: 'test' })
  class User extends VuexModule {
    public name: string = ''
    @Mutation
    public setName(newName: string): void {
      this.name = newName
    }
    @Action
    public updateName(newName: string): void {
      this.context.commit('setName', newName)
    }
  }
  export default User
  ```
  在`vuex-module-decorators`库中提供了`Module`, `Mutation` 和 `Action`装饰器，对于`Actions`，在 `Mutations`和`context`中，我们不需要将状态作为我们的第一个参数，这个第三方库库会处理这些。这些方法已经自动注入了。
- 在store文件夹下，我们需要创建一个`index.ts` 来初始化`vuex`以及注册这个`module`：
  ```ts
  import Vue from 'vue'
  import Vuex from 'vuex'
  import User from '@/store/modules/user'
  Vue.use(Vuex)
  const store = new Vuex.Store({
    modules: {
      User
    }
  })
  export default store
  ```
- 在组件中使用 Vuex
  要使用Vuex，我们可以利用第三方库`vuex-class`。该库提供装饰器使得在我们的Vue组件中绑定 `State`，`Getter`，`Mutation`和`Action`。
  由于我们正在使用命名空间的Vuex模块，因此我们首先从`vuex-class` 引入 `namespace`，然后传递模块名称以访问该模块。
  ```vue
  <template>
    <div class="details">
      <div class="username">User: {{ nameUpperCase }}</div>
      <input :value="name" @keydown="updateName($event.target.value)" />
    </div>
  </template>
  <script lang="ts">
  import { Component, Vue } from 'vue-property-decorator'
  import { namespace } from 'vuex-class'
  const user = namespace('user')
  @Component
  export default class User extends Vue {
    @user.State
    public name!: string

    @user.Getter
    public nameUpperCase!: string

    @user.Action
    public updateName!: (newName: string) => void
  }
  </script>
  ```

### Axios 封装
在Vue的项目中，我们使用 axios 来发送 AJAX 请求，我在项目里写了 axios 的[统一拦截器](https://github.com/dreamqyq/cnode-community/blob/master/src/utils/request.ts)，这里的拦截器写法和 JS 没有任何区别，但是在使用该拦截器发送请求的方法会有一些不同之处，具体代码可以参考项目中的[api请求代码](https://github.com/dreamqyq/cnode-community/blob/master/src/api/index.ts) 。下面我贴一段代码简单介绍一下：
```ts
export function getTopicLists(
  params?: TopicListParams
): Promise<Array<TopicListEntity>> {
  return request.get("topics", {
    params
  });
}
```
使用TypeScript，最重要的就是类型，所以在上述代码中，传进来的参数规定类型为`TopicListParams` ，而函数返回的参数是`Promise<Array<TopicListEntity>>`，这样我们在调用`getTopicLists`的时候，就可以写成这样：
```ts
// 使用await
const response = await getTopicLists(); // response 即返回的Array<TopicListEntity>
// 或使用promise.then
await getTopicLists({
    limit: 40,
    page
  }).then(response => {
    // response 即返回的Array<TopicListEntity>
  })
});
```
另外：一般来说后端传给前端的响应体，我们应该添加一个`interface`类型来接收，就上面代码中的`TopicListEntity`，如果后端传过来的响应数据很多，手写`interface`就很麻烦，所以给大家推荐一个工具，可以根据 json 自动生成 TypeScript 实体类型：[json to ts](http://www.jsontots.com/)。

---

## 在Vue中写TSX有哪些需要注意的地方
### v-html
使用`domPropsInnerHTML`来替代`v-html`
```html
<main
    domPropsInnerHTML={this.topicDetail.content}
    class="markdown-body"
>
    loading💤💤
</main>
```
### v-if
使用三元操作符来替代`v-if`
```jsx
 {this.preFlag ? <button class="pageBtn">......</button> : ""}
```
### v-for
使用`map`遍历替代`v-for`
```jsx
{this.pageBtnList.map(page => {
  return (
    <button
      onClick={this.changePageHandler.bind(this, page)}
      class={[{ currentPage: page === this.currentPage }, "pageBtn"]}
    >
      {page}
    </button>
  );
})}
```

### render
**注意**：在render函数中的组件名一定用**kebab-case命名**
```jsx
protected render() {
  return (
    <footer>
      <hello-word />
      <p>
        &copy; 2020 Designed By Enoch Qin
        <a href="https://github.com/dreamqyq/cnode-community" target="_blank">
          源码链接 GitHub >>
        </a>
      </p>
    </footer>   
  );
}
```
### onClick事件传值（TSX）
使用`template`的时候，如果用`v-on`绑定事件，想要传参的话，可以直接这么写：
```vue
<button @click="clickHandle(params)">click me</button>
```
但是在TSX中，如果直接这么写，就相当于立即执行了clickHandle函数：
```jsx
render(){
  // 这样写是不行的！！
  return <button onClick={this.clickHandler(params)}>click me</button>
}
```
因此，我们不得不使用`bind()`来绑定参数的形式传参：
```jsx
render(){
  return <button onClick={this.clickHandler.bind(this, params)}>click me</button>
}
```

---
## 开发过程中遇到的问题及解决
### Router history模式
原CNode社区的url是没有#的history模式，但是这需要后端支持，所以本项目中使用了hash模式。
- Vue Router 默认模式是hash模式，页面url长这样： localhost:9090/#/payIn
  如果改成history模式，url就变成了（没有了#） localhost:9090/payIn
- vue-router 默认 hash 模式 —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。
  如果不想要很丑的 hash，我们可以用路由的 history 模式，这种模式充分利用 history.pushState API 来完成 URL 跳转而无须重新加载页面。
  ```js
  const router = new VueRouter({
    mode: 'history',
    routes: [...]
  })
  ```
- 当你使用 history 模式时，URL 就像正常的 url，例如 [http://yoursite.com/user/id](http://yoursite.com/user/id)，也好看！
  不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问 [http://oursite.com/user/id](http://oursite.com/user/id) 就会返回 404，这就不好看了。
所以呢，你要在服务端增加一个覆盖所有情况的候选资源：如果 URL 匹配不到任何静态资源，则应该返回同一个 index.html 页面，这个页面就是你 app 依赖的页面。

### publicPath 部署应用包时的基本URL
*   默认情况下【 / 】，Vue CLI 会假设你的应用是被部署在一个域名的根路径上，例如 [https://www.my-app.com/](https://www.my-app.com/)。
*   如果应用被部署在一个子路径上，你就需要用这个选项指定这个子路径。例如，如果你的应用被部署在 [https://www.my-app.com/my-app/](https://www.my-app.com/my-app/)，则设置 publicPath 为 【/my-app/】。
*   这个值也可以被设置为空字符串【 (‘')】 或是相对路径【 ('./‘)】，这样所有的资源都会被链接为相对路径，这样打出来的包可以被部署在任意路径，也可以用在类似 Cordova hybrid 应用的文件系统中。

### <base> 标签
在项目最开始开发的时候，出现了子页面无法刷新（刷新就会报错：`Uncaught SyntaxError: Unexpected token '<‘`），并且子页面用到的图片资源找不到的问题。通过stack overflow的这个[问题的答案](https://stackoverflow.com/questions/51210795/vue-cli-uncaught-syntaxerror-unexpected-token)，使用`<base>`标签成功解决了这个问题。
`<base>`标签是用来指定一个HTML页中所有的相对路径的根路径，在`/public/index.html`中添加标签`<base href="./" />`，设置 href为相对路径，在本地调试和打包上线的时候，资源就都不会出问题啦。

### Axios withCredentials
在本项目中，后端调用的是 cnode 提供的[后端接口](https://cnodejs.org/api)，所有接口的都设置了`Access-Control-Allow-Origin: *`，用来放置跨域。但是如果我们将axios 的 withCredentials（**表示跨域请求时是否需要使用凭证**）设置成true，会包CORS跨域错误：
 原因是：`Access-Control-Allow-Origin`不可以为 `*`，因为 `*` 会和 `Access-Control-Allow-Credentials:true` 产生冲突，需配置指定的地址。
因此在项目中，`withCredentials`设置成`false`即可。

### Github-markdown-css
在项目中使用到了`github-markdown-css`这个库用于展示markdown的样式。用法如下：
- 在`main.ts`引入 `import "github-markdown-css"`
- 在`App.vue`中添加如下样式：
  ```css
  .markdown-body {
    box-sizing: border-box;
    min-width: 200px;
    max-width: 1400px;
    margin: 0 auto;
    padding: 45px;
  }
  
  @media (max-width: 767px) {
    .markdown-body {
      padding: 15px;
    }
  }
  ```
- 在包含markdown内容的父标签添加class：`markdown-body`

## 总结
> Now you have all the basic information you need to create a Vue.js application completely in TypeScript using a few official and third-party libraries to fully leverage the typing and custom decorator features. Vue 3.0 will have better support for TypeScript out of the box, and the whole Vue.js code was rewritten in TypeScript to improve maintainability.
Using TypeScript might seem a bit overwhelming at first, but when you get used to it, you’ll have far fewer bugs in your code and smooth code collaboration between other developers who work on the same code base. （摘自[How to write a Vue.js app completely in TypeScript](https://blog.logrocket.com/how-to-write-a-vue-js-app-completely-in-typescript/)）

翻译：现在，您知道了在创建Vue.js + TypeScript应用程序的过程中，如何使用几个官方库和第三方库所需的所有基本信息，以充分利用**类型**和**自定义装饰器**。已经发布了公测版本的Vue 3.0开箱即用将更好地支持TypeScript，并且整个Vue.js的项目代码都使用TypeScript进行了重写，以提高可维护性。
刚开始使用TypeScript似乎有点让人不知所措，但是当您习惯了它之后，您的代码中的错误将大大减少，并且，在同一个项目中可以和其他开发者更好的协同工作。

---
> 本文参考资料：
👉[https://blog.logrocket.com/how-to-write-a-vue-js-app-completely-in-typescript/](https://blog.logrocket.com/how-to-write-a-vue-js-app-completely-in-typescript/) 
👉[https://zhuanlan.zhihu.com/p/99343202](https://zhuanlan.zhihu.com/p/99343202)
👉[TypeScript 支持 — Vue.js](https://cn.vuejs.org/v2/guide/typescript.html)
👉[TypeScript 官网](https://www.typescriptlang.org/docs/handbook/basic-types.html)
👉[https://segmentfault.com/a/1190000016837020](https://segmentfault.com/a/1190000016837020)


（完）