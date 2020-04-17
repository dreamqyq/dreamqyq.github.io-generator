---
title: "网页中的登录注册功能是如何实现的"
date: 2018-08-21
draft: false
categories: ["HTTP"] 
tags: ["HTTP"]
---

**摘要**：登录注册是一个网站最常用的也是最基本的功能，本文就来详细的介绍一下在网页中，实现这两个最基本的需求从后端到前端需要做些什么。

注：本文所提到的所有代码都已上传至[github](https://github.com/dreamqyq/ServerTest) ，可以直接clone后在终端使用测试。

## 一、前置知识：
- 基础知识可见我之前的博客：[HTTP入门（请求与响应的基本知识）](https://www.jianshu.com/p/109de7b74813) 。首先我们至少需要了解HTTP最基本的**request请求**和**response响应**的相关概念。

- 再看一下服务端和客户端之间的交互，如下图：

![](https://upload-images.jianshu.io/upload_images/11827773-02406eae008e74ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

用文字描述一下：
1. 客户端（即浏览器）向服务端发起request请求
2. 服务端通过监听80端口（http）或443端口（https）来接收请求
3. 服务端接收请求后进行相关处理
4. 服务端向客户端发response响应。
5. 客户端接收response响应，完成一次交互。

- 需要了解用JS如何向服务端发请求，举个例子：
```js
let xhr = new XMLHttpRequest()
xhr.open('POST','/')
xhr.setRequestHeader('content-type','x-www-form-urlencoded')  // 设置请求头
xhr.onreadystatechange = function(){
  if(request.readyState === 4){ // 整个请求过程已完成
    if(request.status >=200 && request.status < 400){ // 状态码在200到400之间，说明请求成功
      successFn.call(undefined,request.responseText) //调用成功函数
    }else{
      failFn.call(undefined,request.status) // 调用失败函数
    }
  } 
}
xhr.send(body) // 发送请求体
```

- 需要知道后端如何发出响应（Nodejs实现）
```js
// 注：Nodejs中没有path、method、response这三个api，需要另外获取
// 这里的代码旨在我们了解后端需要做的事情，不用深入研究
if(path === '/' && method === 'GET'){
    var string = fs.readFileSync('./index.html','utf-8') // 读取服务端文件
    response.statusCode = 200 // 设置响应状态码
    response.setHeader('Content-Type','text/html;charset=utf-8') // 设置响应头
    response.write(string) // 设置响应体
    response.end() // 本次响应结束
}
```

## 二、注册具体流程

### 1、首先前端需要做一个注册的页面
像这样：
![](https://upload-images.jianshu.io/upload_images/11827773-7fd830c6854fb97b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2、然后前端需要在用户提交数据后，获取到这些数据
```js
let $form = $('#register-form')
$form.on('submit', function(e) {
  e.preventDefault() // 阻止默认事件，因为form的submit事件默认会刷新页面
  let hash = {}
  let need = ['email', 'password', 'repassword']
  need.forEach((name) => { // 通过遍历获取表单数据并存到hash里
    let value = $form.find(`[name=${name}]`).val()
    hash[name] = value
  })
})
```

### 3、前端需要将上一步所获得的数据通过post请求发送给服务端
（为什么不是get请求呢，主要还是因为post相对来说更安全）
```js
$.post('/register', hash) // 第一个参数与是路径，第二个参数要传给服务端的数据
  .then(() => {
    console.log('success')
  }, () => {
    console.log('error')
  })
```

### 4、后端接收前端发来的请求并从中读取到需要的信息（如传过来的邮箱密码）
**注：实际开发中密码肯定是不能被后台获取到的**，这里只是举例说明整体的流程。

- 得到post请求的数据，Node没有直接读取请求体的api，所以先封装一个函数。（因为客户端向服务端发送post请求传输数据时，实际上是一段一段传的，所以无法直接获取数据。）
```js
function readBody(request){
  return new Promise((resolve, reject)=>{
    let body = [] // 请求体
    // 监听request的data事件，每接收到一段data，就放到请求体这个哈希表里面
    request.on('data', (chunk) => {
      body.push(chunk);
    }).on('end', () => {
      // 当数据全部接收完时，会触发end事件，然后就可以把所有的数据都得到啦
      body = Buffer.concat(body).toString();
      resolve(body)
    })
  })
}
// 调用上面的函数，得到的请求体类似于这种形式：
// email=1&password=2&repassword=3
```
- 后端通过上面的函数获得了如：`email=1&password=2&repassword=3`这样的字符串，然后需要通过`split`方法将字符串按照所需要的结构解析出来。如：
```js
body =  'email=1&password=2&repassword=3'
let strings = body.split('&') 
// 结果为 ['email=1', 'password=2', 'password_confirmation=3']
```

### 5、简单的数据校验
后端拿到数据后，进行简单的数据校验，出错后将错误信息返回给前端，前端进行解释展示给用户。

- 后端简单校验：
```js
// 默认 @ 符号会转义成 %40 ，所以需要decodeURIComponent解码URI
hash[key] = decodeURIComponent(value) 
// 然后才可以检测email 中是否存在 @ 符号
if (email.indexOf('@') === -1) { // 邮箱中没有@说明邮箱格式错误
  response.statusCode = 400
  response.setHeader('Content-Type', 'application/json;charset=utf-8')
  // 如果出错响应体返回JSON对象，前端就可以直接解析
  response.write(`{
    "errors": {
      "email": "invalid"
    }
  }`)
} else if (password !== repassword) { // 两次输入的密码不一致
  response.statusCode = 400
  response.write('password not match')
}
```
- 前端解析错误信息展示给用户
```js
$.post('/register', hash)
.then((response)=>{
  console.log(response)
}, (response)=>{
  // 获得后端传过来的响应体内容
  // 因为响应体一定是字符串，故需要使用JSON.parse将其转换成对象
  let {errors} = JSON.parse(response.responseText)
  if(errors.email && errors.email === 'invalid'){
    $form.find('[name="email"]').siblings('.error')
    .text('邮箱格式错误')
  }
})
```
效果如下：

![](https://upload-images.jianshu.io/upload_images/11827773-0d71fcda169dc562.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 当用户没有填信息就提交时，就需要前端进行校验了，这种情况下应该直接提示用户填信息而不用再次给后端发请求：
```js
// 先将所有错误提示清空
$form.find('.error').each((index, span)=>{
  $(span).text('')
})
// 邮箱、密码和确认密码没有填数据时，如：
if(hash['email'] === ''){
  $form.find('[name="email"]').siblings('.error')
    .text('填邮箱呀同学')
  return
}
// 密码和确认密码填写的数据不一致时：
if(hash['password'] !== hash['repassword']){
  $form.find('[name="repassword"]').siblings('.error')
    .text('密码不匹配')
  return
}
```
效果如下：
![](https://upload-images.jianshu.io/upload_images/11827773-02da14c91c499a42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 关于前端校验和后端校验
看到这里你会发现，其实前端和后端都可以的进行简单的数据校验，但是要注意的是：**前端可以没有表单校验的过程**（当然有一些简单的数据校验更好），但是**后端必须得有数据校验**。主要原因是：**前端的数据校验是很容易被绕过去的**（比如curl 命令可以绕过前端直接向后台发请求），我们不能过于依赖网页中的JS校验，必须得有后台的数据校验来确保数据的安全。

### 6、注册成功后将用户数据传到数据库中

**注：实际开发中不能将密码直接传到数据库存储，数据库中应该存的是用户的密码加密后的数据，然后用户如果登录，服务端得到密码，将其加密后再和数据库中的信息做对比**，不过加密操作比较复杂，本文暂时不作演示，使用明文密码来进行操作。
```js
var users = fs.readFileSync('./database/users', 'utf8') // 获取数据库中的数据
try {
  users = JSON.parse(users) // 将数据库中的数据转换成JSON对象
} catch (exception) { // 如果转化失败直接让users等于空数组
  users = []
}
users.push({ // 将数据push到数据库中
    email: email,
    password: password
})
var usersString = JSON.stringify(users) // 将JSON对象字符串化后再进行存储
fs.writeFileSync('./database/users', usersString) // 将数据写入数据库
response.statusCode = 200
```
### 7、后端校验：邮箱是否被注册
比如某一个邮箱已经被注册了，服务端应该先检索数据库中是否存在该邮箱的账号，若存在，应该提示用户然后阻止用户再次使用相同的邮箱进行注册。
```js
let inUse = false // 声明变量 isUse ，用于表示数组是否被注册过
for (let i = 0; i < users.length; i++) {
  // 遍历数据库中的原有数据，如果邮箱存在，isUse 为 true
  let user = users[i]
  if (user.email === email) {
    inUse = true
    break;
  }
}
if (inUse) {
  // 如果邮箱已被注册，返回状态码400，返回响应体 'email in use'
  response.statusCode = 400
  response.write('email in use')
} else {
  // 此时数据库中没有用户输入的邮箱，于是将本次数据传入数据库
  users.push({
    email: email,
    password: password
  })
  var usersString = JSON.stringify(users)
  fs.writeFileSync('./database/users', usersString)
  response.statusCode = 200
}

```

## 三、登录具体流程

### 1、前端先写一个登录页面
就像这样：

![](https://upload-images.jianshu.io/upload_images/11827773-c12aa5325f0a5bbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 2、前端需要将用户写的登录信息拿到并传给数据库，并会做一些简单的数据校验
因为登录功能的这部分前端代码和注册类似，这里就不再赘述，整体的前端代码如下：
```js
let $form = $('#login-form')
$form.on('submit', function(e) {
  e.preventDefault()
  let hash = {}
  let need = ['email', 'password']
  need.forEach((name) => {
    let value = $form.find(`[name=${name}]`).val()
    hash[name] = value
  })
  $form.find('.error').each((index, span) => {
    $(span).text('')
  })
  if (hash['email'] === '') {
    $form.find('[name="email"]').siblings('.error')
      .text('填邮箱呀同学')
    return
  }
  if (hash['password'] === '') {
    $form.find('[name="password"]').siblings('.error')
      .text('填密码呀同学')
    return
  }
  $.post('/login', hash)
    .then((response) => {
        // 登陆成功直接跳转到首页
       window.location.href = '/'
    }, (request) => {
      let { errors } = request.responseJSON
      console.log(errors)
      // 如果得到响应提示fail，则说明经过后端验证，登录的邮箱不存在或者密码错误
      // 然后设置错误信息提示用户
      if (errors && errors === 'fail') {
        $form.find('[name="password"]').siblings('.error')
          .text('邮箱或密码错误')
      }
    })
})
```

### 3、后端从数据库中验证用户信息

```js
let found
for (let i = 0; i < users.length; i++) {
  // 遍历数据库，看用户所传的邮箱和密码是否和数据库中的数据匹配
  if (users[i].email === email && users[i].password === password) {
    // 如果匹配，fond为true
    found = true
    break
  }
}
if (found) { 
  // 如果从数据库中找到了该用户
  response.statusCode = 200
  response.write('success')
} else {
  // 如果没找到用户信息，说明邮箱不存在或者密码错误
  // 返回状态码401，表示认证失败
  response.statusCode = 401
  response.setHeader('Content-Type', 'application/json;charset=utf-8')
  // 并返回一个JSON对象，方便前端读取错误信息并提示用户
  response.write(`{
    "errors": "fail"
  }`)
}
```

### 4、后端设置cookie

这里暂不解释cookie的概念，深入了解可以看我的这一篇博客[浏览器缓存控制详解](https://www.jianshu.com/p/9ed3e8759ce3)。
目前你只需要理解cookie就是个请求头，他会告诉服务器你这个用户到底是谁即可。
```js
 if(found){
    // 设置cookie
   response.setHeader('Set-Cookie', `login_email=${email}`)
   response.statusCode = 200
   response.write('success')
 }
```
设置cookie后，再看注册页面的响应头多了个东西，这就是我们刚才设置的cookie：

![](https://upload-images.jianshu.io/upload_images/11827773-221ec5fea8924e0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后跳转到首页，再看首页的请求头：

![](https://upload-images.jianshu.io/upload_images/11827773-0125fc26cd6fe8f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以我们可以得出，**只要在某一个页面设置了`Set-Cookie`头，那么从此以后，只要是相同的源（即同一个域名）的请求，在请求头里面都会带上你设置的cookie。**

### 5、后端通过读取cookie响应对应的用户信息

下面的例子是将用户的邮箱账号显示在首页中：
```js
let cookies = ''
// 如果有多个cookie，会这始终形式：'login_email=1@enoch.com ; a=1 ; b=2'
// 通过split方法将其分离成我们需要的形式
if (request.headers.cookie) {
  cookies = request.headers.cookie.split('; ') // ['login_email=1@enoch.com', 'a=1', 'b=2']
}
let hash = {}
for (let i = 0; i < cookies.length; i++) {
  let parts = cookies[i].split('=')
  let key = parts[0]
  let value = parts[1]
  hash[key] = value
}
// 上述操作最终会得到 hash = {login_email:'1@enoch.com',a:'1',b:'2'}
let email = hash.login_email
let users = fs.readFileSync('./database/users', 'utf8')
users = JSON.parse(users)
let foundUser
for (let i = 0; i < users.length; i++) {
  if (users[i].email === email) {
    foundUser = users[i]
    break
  }
}
if (foundUser) {
  // 如果找到该用户的信息，就将首页的'__username__'替换成用户的邮箱
  string = string.replace('__username__', foundUser.email)
} else {
  // 如果没有找到该用户的信息，就将首页的'__username__'替换成N/A
  string = string.replace('__username__', 'N/A')
}
```

（END）

