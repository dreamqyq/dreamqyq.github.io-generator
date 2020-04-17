---
title: "浏览器缓存控制详解（cookie、session、localStorage、Cache-Control等）"
date: 2018-08-22
draft: false
categories: ["HTTP"] 
tags: ["HTTP"]
---

**摘要**：本文将会详细的介绍浏览器实现缓存控制的相关知识，包括cookie、session、localStorage、Cache-Control、Expires、ETag、Last-Modified等概念。

注：在看本文之前建议可以先看一下[网页中的登录注册功能是如何实现的](https://www.jianshu.com/p/42a886492c42)，对前端和后端的数据交互有个大概的了解。

## 1、cookie是什么 && cookie能干什么

- 给cookie一个定义吧：
>Cookie（复数形态Cookies），中文名称为“小型文本文件”或“小甜饼”。指某些网站为了辨别用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）。 ——摘自维基。

- 如何通俗的解释：
  1. Cookie 是浏览器访问服务器后，服务器传给浏览器的一段数据。
  2. 浏览器需要保存这段数据，不得轻易删除。
  3. 此后每次浏览器访问该服务器，都必须带上这段数据。

- 所以就能得出cookie的特点：
  1. 服务器通过设置`Set-Cookie`**响应头**来设置 cookie
  0. 浏览器得到 cookie 后，每次**同源的请求的请求头**都会带上 cookie
  0. 服务器读取 cookie 就知道了登录用户的信息（如账户名等）
  0. cookie 实际上存储在**本地计算机的硬盘里**
  0. cookie 的最大储存量一般只有**4K**

- 那么cookie有哪些缺点呢：
  1. Cookie**很容易被用户篡改**（ Session 可以解决这个问题，防止用户篡改）
  0. Cookie 的默认有效期理论上在**用户关闭页面后就失效**，实际上在在20分钟左右，不同浏览器策略不同。但是后端可以强制设置有效期（如何设置见下文）。
  0. Cookie 也有一定的**同源策略**，不过跟 AJAX 的同源策略稍微有些不同。如：
      - 当请求 qq.com 下的资源时，浏览器会默认带上 qq.com 对应的 Cookie，不会带上 baidu.com 对应的 Cookie
      - 当请求 v.qq.com 下的资源时，浏览器不仅会带上 v.qq.com 的Cookie，还会带上 qq.com 的 Cookie
      - 另外 Cookie 还可以根据路径做限制，请自行了解，这个功能用得比较少。

- 有了Cookie，我们就可以实现这两件事 ：
  - 第一个作用是**识别用户身份**。如：
比如用户 A 用浏览器访问了http://a.com，那么http://a.com 的服务器就会立刻给 A 返回一段数据「uid=1」（这就是 Cookie）。当 A 再次访问 http://a.com的其他页面时，就会附带上「uid=1」这段数据。这样服务端就知道 A 是谁了。

  - 第二个作用是**记录历史**。如：
假设 http://a.com 是一个购物网站，当 A 在上面将商品 B1 、B2 加入购物车时，JS 可以改写 Cookie，改为「uid=1; cart=B1,B2」，表示购物车里有 B1 和 B2 两样商品了。这样一来，当用户关闭网页，过三天再打开网页的时候，依然可以看到 B1 、B2躺在购物车里，因为浏览器并不会无缘无故地删除这个 Cookie。

  - （需要注意的是：上面的例子只是为了让大家了解 Cookie 的作用而构想出来的，实际的网站使用 Cookie 时会更谨慎一些。为什么要非常谨慎的使用cookie请从下文寻找答案。）

- 那么我们如何设置cookie呢：
其实只要一句话：**在响应头中设置`Set-Cookie`即可**，详见[Set-Cookie MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie) 。具体参数如下：
  - `Set-Cookie: <cookie-name>=<cookie-value> ` <br>
普通的cookie，所有参数默认
  - `Set-Cookie: <cookie-name>=<cookie-value>; Expires=<date>`   <br>
cookie 的最长有效时间，形式为符合 HTTP-date 规范的时间戳。
  - `Set-Cookie: <cookie-name>=<cookie-value>; Max-Age=<non-zero-digit>`  <br>
在 cookie 失效之前需要经过的秒数。一位或多位非零（1-9）数字。假如二者 （指 Expires 和Max-Age） 均存在，那么 Max-Age 优先级更高。
  - `Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>` <br>
指定 cookie 可以送达的主机名。假如没有指定，那么默认值为当前文档访问地址中的主机部分（但是不包含子域名）。与之前的规范不同的是，域名之前的点号会被忽略。假如指定了域名，那么相当于各个子域名也包含在内了。
  - `Set-Cookie: <cookie-name>=<cookie-value>; Path=<path-value>` <br>
指定一个 URL 路径，这个路径必须出现在要请求的资源的路径中才可以发送 Cookie 首部。
  - `Set-Cookie: <cookie-name>=<cookie-value>; Secure` <br>
一个带有安全属性的 cookie 只有在请求使用SSL和HTTPS协议的时候才会被发送到服务器。（注意：非安全站点（http:）已经不能再在 cookie 中设置 secure 指令了）
  - `Set-Cookie: <cookie-name>=<cookie-value>; HttpOnly` <br>
设置了 HttpOnly 属性的 cookie 不能使用 JavaScript 经由  `Document.cookie` 属性、`XMLHttpRequest` 和  `Request`APIs 进行访问，以防范跨站脚本攻击（XSS）。
  - `Set-Cookie: <cookie-name>=<cookie-value>; SameSite=Strict`
  - `Set-Cookie: <cookie-name>=<cookie-value>; SameSite=Lax` <br>
上面两个允许服务器设定一则 cookie 不随着跨域请求一起发送，这样可以在一定程度上防范跨站请求伪造攻击（CSRF）。

- 如何删除cookie <br>
 通过设置cookie的有效期在**当前时间之前**，就可以删除cookie啦
     ```js
      var delete_cookie = function(name) {
          document.cookie = name + '=;expires=Thu, 01 Jan 1970 00:00:01 GMT;';
      };
     ```

## 2、使用session保存信息

-  先举个例子看如何使用session
之前的写法：直接将数据放到cookie里面：
    ```js
    response.setHeader('Set-Cookie', `login_email=${email}`)
    ```
    这样写一来会暴露用户的个人信息，二来用户可以直接通过浏览器修改cookie，极有可能获取到别人的用户信息，极不安全。因此出现了下面的使用session的操作：
    ```js
    let sessions = {}
    let sessionId = Math.random() * 10000 // 设置sessionId 为一个随机数
    sessions[sessionId] = {login_email:email} // 将email 存储在sessions这个对象中
    response.setHeader('Set-Cookie', `sessionId = ${sessionId}`) 
    // cookie中存储的是 sessionId 这个随机数
    ```
      首先来解释一下上面的代码：即设置`cookie`的中存储的值为一个随机数，当后台获取到`cookie`时，就可以获取到该随机数并在`sessions`这个对象中查找`key`为这个随机数的`value`，即可知道用户的邮箱是什么 。如图所示，此时的响应头是这样的：

![](https://upload-images.jianshu.io/upload_images/11827773-102f43251dd8a884.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- 那么究竟如何定义session呢：
首先我们得知道session的本质：就是**存储在服务器上的一个哈希表**。
  1. 将**sessionID（随机数）** 通过 **Cookie** 发给客户端
  2. 客户端访问服务器时，服务器读取 sessionID
  3. 服务器有一块**内存（哈希表）**保存了所有 session
  4. 通过sessionID 后台可以得到对应用户的隐私信息，如 id，email
  5. 这块内存（哈希表）就是服务器上的所有 session

- 那么session相对于cookie就没有缺点了吗？
很明显，答案是否定的。**session最大的缺点就是占内存**，如果你的用户量非常大，服务器就需要足够的容量来存储数据。

- 不基于cookie能否实现session呢
答案是肯定的，可以通过设置网址的**查询参数** + **localStorage** 来达成目的。具体过程如下：
  1. 后端待用户登陆后设置他的sessionID，但不把它放在 cookie 里，而是将信息通过响应体传JSON给前端。
  0. 前端拿到响应体中的JSON后将其转换成对象（`JSON.parse`）
  0. 将从JSON中获取到的数据（如 sessionID）放在 localStorage 里面（localStorage里的数据目前暂时用不到）
  0. 以后跳转到其他页面（如首页）时，将 sessionID 放在 URL 的查询参数里（如：` window.location.href = '/?sessionId=object.sessionId' `）
  0. 那么进入首页后，该页面的 URL 的查询参数就带上了你的sessionID
  0. 后端通过在用户访问首页时，传到服务器的 URL ，来获取到查询参数，从而获取到用户的 sessionID，然后在数据库中查到sessionID对应的信息就可以知道用户是谁。

## 3、localStorage是什么 && localStorage 怎么用

localStorage 是HTML5 技术提供的api ，是window对象下的一个方法（`window.localStorage`）。

localStorage 的实质是一个**存储在计算机本地的哈希表**。

- localStorage常用的api
  - `localStorage.setItem('myCat', 'Tom')`
访问当前域名下的本地 `Storage` 对象，并增加了一个数据项通过使用`Storage.setItem()` 作为 Storage 接口的方法，接受一个键名和值作为参数，将会把键名添加到存储中，如果键名已存在，则更新其对应的值。
  - `let cat = localStorage.getItem('myCat')`
该语法用于读取 localStorage 项
  - `localStorage.removeItem('myCat')` 
该语法用于移除 localStorage 项
  - `localStorage.clear()`
该语法用于移除所有的 localStorage 项

- 需要注意的是：**localStorage 只能存 string，所以如果想存Object，就需要用JSON来存**，举个例子：
`localStorage.setItem('jsonObj',JSON.stringify({name:'obj'}))`
然后如果想要解析这个 JSON 将其转换成对象可以使用 `JSON.parse()`

- 那么localStorage怎么使用呢：
  - 首先我们需要知道JS中的变量有这样的问题：**变量只在当前的会话期内有效**。即只要刷新页面，之前存储的变量就被回收了。那么我们如何让之前所存储的变量在刷新页面之后还存在呢，所以就有了localStorage 来解决这个需求。
  - 来看一个小demo，这个demo通过使用 localStorage 完成了变量的**持久化存储**，因为localStorage实际上是**存储在本地计算机中的**，不会因为页面刷新就导致变量被回收。
    ```js
    let a = localStorage.getItem('a')
    if(!a){
      a = 0
    }esle{
      a = parseInt(a,10) + 1 // 如果不进行转换，将会变成字符串的相加
    }
    console.log(a)
    localStorage.setItem('a',a)
    // 每次刷新页面 ，a 的值都会加 1
    ```
  -  常见的使用情景：用户第一次进入页面时提示用户一些信息，第二次进入以后就不再提示。  示例代码如下：
      ```js
      let already = localStorage.getItem('isPrompt')
      if(!already){
        alert('我们的页面改版啦')
        localStorage.setItem('isPrompt', true)
      }esle{
          // 已经提示了就什么也不做
      }
      ```

- localStorage的特点
  1. localStorage 与 HTTP **无关**
  0. HTTP **不会带上** localStorage 的值
  0. 只有**相同域名**的页面才能互相读取 localStorage（遵循同源策略）
  0. 每个域名 localStorage 最大存储量为 **5Mb** 左右（每个浏览器不一样）
  0. 常用场景：记录有没有提示过用户（记录一些不敏感的信息）
  0. localStorage 理论上**永久有效**，除非用户清理缓存，无法设置过期时间 

- sessionStorage 和 localStorage 有什么关系和区别
  - 上一条记录的 localStorage 的特点，1~4条二者都有。
  - 区别：sessionStorage 在**用户关闭页面后**（即 session 结束后或者说会话结束后）就失效，而且没办法控制；而 localStorage 理论上**永久有效**。
  - sessionStorage 的 api 和 localStorage 完全一样。如`setItem()`、`getItem()`、`remove()`、`clear()`

## 4、关于上面的概念需要注意的事情

- cookie 和 session 有什么关系：
答：一般来说  cookie 是基于 session 实现的。cookie是存在客户端本地的，而session是保存在服务器上的。

- cookie 和 localStorage 的区别是什么
答：cookie **每次请求会被带给服务器**，而 localStorage不会；cookie的最大储存量一般只有**4k**，而localStorage 一般有**5Mb** ；cookie的有效期一般在**用户关闭页面后就失效**，而localStorage理论上**永久有效**。

- 可能你就要问了，看起来cookie 和 localStorage 几乎没有关系，为什么要放在一起比较？ <br>
这是由历史原因的：localStorage 是新API，在它出现以前，之前的前端如何实现跨页面的数据持久化存储呢？只能通过cookie  ，要知道cookie 和 localStorage 都是存放在计算机本地，所以当时很多程序员都把数据放在cookie里，但是有个问题，你所有存在 cookie里面的东西，每次请求都会带到服务器里面去，如果cookie里面放的东西太多，那么每次请求就要花费更多的时间。**所以要达到跨页面的数据持久化存储，最优解就是使用 localStorage**。

- **前端永远不要读或者写cookie，读写cookie一系列的操作是后端需要完成的工作**。（这里的前后端指的是代码上的分离而不是人员上的分离）

## 5、HTTP缓存 && Cache-Control 缓存控制
需要提一句的是：**缓存控制其实也是前端性能优化的一部分**，所以这一点其实很值得关注。

缓存控制的使用场景：一些网站需要加载的资源很多，导致每次刷新页面速度都非常都慢，那么该如何加快请求速度，缓存控制就应运而生。

[Cache-Control](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control)   通用消息头被用于在http 请求和响应中通过指定指令来实现缓存机制。缓存指令是单向的, 这意味着在请求设置的指令，在响应中不一定包含相同的指令。

最常用的响应指令：`Cache-Control: max-age=<seconds>` ：
- 设置缓存存储的最大周期，超过这个时间缓存被认为过期(单位秒)。与Expires相反，时间是相对于请求的时间。即在设置的时间内，请求**相同的URL**将不会把请求发送到服务器，浏览器会阻断这个请求（即**这个请求实际并没有发送出去**），然后然后之间展示缓存过的上一次的数据（存储在本地的硬盘或内存）。实际开发中，我们一般设置为**一年31536000秒以上**。
- 需要注意的是：一般**首页**（甚至包括所有的html页面）**不应该设置缓存**（chrome浏览器甚至会直接禁用这个设置，也就是说你给首页设置Cache-Control也不会生效）。因为如果你连首页都设置了缓存，用户即使刷新页面，也不会向服务器发送任何请求，那么如果你的代码更新了，用户在缓存期间将始终无法获取到最新的版本。
- 实际开发中的使用：**html页面不设置Cache-Control**，其他资源Cache-Control设置**一年以上**，如果代码更新（比如js或css），该资源的请求的url的查询参数加上一个版本号，或者文件名后加随机数即可。也就是说，只要你的请求的url有一点点不一样，就不会使用缓存


## 6、Expires 是什么 && 有什么不好的地方

- Expires 也是用来控制缓存的，但是现在在开发中，**优先使用Cache-Control**。
[Expires](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Expires) 响应头包含日期/时间， 即在此时候之后，缓存过期。

- 如何使用Expires呢： <br>
设置响应头，如：`Expires："Wed, 22 Aug 2018 07:43:17 GMT"`
后面接的是格林尼治时间。当过了设置的时间后缓存就过期了。

- 所以 `Expires` 与`Cache-Control`的区别就是：前者设置的是什么时候过期，后者设置的是过了多久过期。

- 需要注意的是：**如果在 `Cache-Control` 响应头设置了 "max-age" 或者 "s-max-age" 指令，那么 `Expires` 头会被忽略**。那么为什么 Cache-Control 优先级比Expire 高呢？

- 因为 Cache-Control 相对于 Expires 是个更新的技术， 而且使用 Expires 是存在bug 的。

- Expires 的bug：时间使用的是计算机本地的时间，如果某一天计算机时间错乱，本地时间一直在你设置的时间之后，那么他就永远使用不了缓存。


## 7、ETag 是什么 && MD5 是什么

首先，Etag 是用来给文件一个版本号的。

那么我们先来了解一下[MD5](https://zh.wikipedia.org/wiki/MD5) 。MD5是一个**消息摘要算法**。MD5 的常见使用场景：你在网上下载一个很大的文件，下载过程中你怎么知道自己下载的对不对呢？所以MD5 就是为了这种情景而生的。即网上的文件除了有资源本身外，还会有一个MD5值，然后你下载到本地后的文件也可以算出一个MD5 值，然后二者对比，如果完全相同则说明下的文件是正确的。

Etag 的使用场景：
1. 后端算出资源的MD5值，将其设置到响应头的Etag里，如： <br>
`Etag："33a64df551425fcc55e4d42a148795d9f25f89d4"`
2. 然后下一次请求时，里面这个资源的请求头就多了一个值： <br>
`If-None-Match: "33a64df551425fcc55e4d42a148795d9f25f89d4"`
3. 后端就有如下设置：
如果请求头的`If-None-Match`中的值和资源的MD5一样，说明资源是最新的，不需要下载，即可以返回[304状态码](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/304)（ Not Modified），然后在此分支下就不用设置响应体了。
4. 如果MD5的值不一样，说明你的资源需要更新，此时再返回最新的资源作为响应体。

**Etag 与 Cache-Control 的区别**：
- Cache-Control 直接不会发请求，只要url一样，直接使用缓存了的数据
- Etag 则每一次都会请求，只不过如果资源的MD5一样，就不下载

## 8、Last-Modified 是什么 && 使用过程是怎么样的

**`Last-Modified`**  是一个响应首部，其中包含源头服务器认定的资源做出修改的日期及时间。 它通常被用作一个验证器来判断接收到的或者存储的资源是否彼此一致。由于精确度比  `ETag` 要低，所以这是一个备用机制。包含有  `If-Modified-Since` 或 `If-Unmodified-Since`首部的条件请求会使用这个字段。

使用过程：
1. 在浏览器第一次请求某一个URL时，服务器端的返回状态会是200，内容是你请求的资源，同时有一个Last-Modified的属性标记在响应头里，此文件在服务期端最后被修改的时间，格式类似这样：
`Last-Modified:Tue, 24 Feb 2009 08:01:04 GMT`
2. 客户端第二次请求此URL时，根据HTTP协议的规定，浏览器会向服务器传送If-Modified-Since请求头，询问该时间之后文件是否有被修改过：
`If-Modified-Since:Tue, 24 Feb 2009 08:01:04 GMT`
3. 如果服务器端的资源没有变化，则自动返回HTTP 304（NotChanged.）状态码，内容为空，这样就节省了传输数据量。当服务器端代码发生改变或者重启服务器时，则重新发出资源，返回和第一次请求时类似。从而保证不向客户端重复发出资源，也保证当服务器有变化时，客户端能够得到最新的资源。

（END）


