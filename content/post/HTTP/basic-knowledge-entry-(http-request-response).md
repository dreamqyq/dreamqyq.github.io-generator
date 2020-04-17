---
title: "HTTP入门（请求与响应的基本知识）"
date: 2018-05-04
draft: false
categories: ["HTTP"] 
tags: ["HTTP"]
---

**摘要**：本文主要介绍HTTP的最基本的知识，包括request请求、response响应、常见的响应状态码、GET请求与POST请求的区别。

## 一、HTTP 请求

### 1. http请求操作/curl命令
- ` curl -s -v -H "Frank: xxx" -- "https://www.baidu.com"//GET获取操作`
请求内容
`````````````````
GET / HTTP/1.1
Host: www.baidu.com
User-Agent: curl/7.54.0
Accept: */*
Frank: xxx

`````````````````
- `curl -X POST -s -v -H "Frank: xxx" -- "https://www.baidu.com"//POST上传操作`
请求内容
````````
POST / HTTP/1.1
Host: www.baidu.com
User-Agent: curl/7.54.0
Accept: */*
Frank: xxx
````````
- `curl -X POST -d "1234567890" -s -v -H "Frank: xxx" -- "https://www.baidu.com"//POST上传带数据`
请求内容
```````
POST / HTTP/1.1
Host: www.baidu.com
User-Agent: curl/7.54.0
Accept: */*
yyy: xxx
Content-Length: 10
Content-Type: application/x-www-form-urlencoded

1234567890
```````
注：第二部分中，至少需要有`Host`、`Content-Length`、`Content-Type`

### 2. 综上所得，请求一共包含以下部分

- 1 动词 路径 协议/版本
2 Key1: value1
2 Key2: value2
2 Key3: value3
2 Content-Type: application/x-www-form-urlencoded
2 Host: www.baidu.com
2 User-Agent: curl/7.54.0
3 
4 要上传的数据

- **其中需要注意的是**：
  1. 请求最多包含四部分，最少包含三部分，即第四部分数据可以为空。
  0. 第三部分永远都是一个回车，用来分隔第二、第四部分。
  0. 第一部分的动词包括`GET`（获取）/`POST`（上传，新增）/`PUT`（整体更新）/`PATCH`（局部更新）/`DELETE`（删除）/`HEAD`（获取资源的元数据）/`OPTIONS`（获取信息，关于资源的哪些属性是客户端可以改变的）等。
  0. 如果没有写路径，则默认为根目录（即 `/`）
  0. 第二部分中的的 Content-Type 标注了第 4 部分的格式
  0. 第一部分的路径，包含查询参数，但不包括锚点


### 3. 使用Chrome浏览器查看http请求

1. 打开 控制台中的Network面板
0. 地址栏输入网址
0. 在 Network 点击，查看 request，点击「view source」
0. 如果有请求的第四部分，那么在 FormData 或 Payload 里面可以看到

举例说明（百度首页请求）：

![百度首页请求](https://upload-images.jianshu.io/upload_images/11827773-64f499129a9c15f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




## 二、HTTP 响应

### 1.http响应出现在请求结束后
如`curl -s -v -H "Frank: xxx" -- "https://www.baidu.com"//GET获取操作`
获得的响应代码为：
````
HTTP/1.1 200 OK
Accept-Ranges: bytes
Cache-Control: private, no-cache, no-store, proxy-revalidate, no-transform
Connection: Keep-Alive
Content-Length: 2443
Content-Type: text/html
Date: Tue, 10 Oct 2017 09:14:05 GMT
Etag: "5886041d-98b"
Last-Modified: Mon, 23 Jan 2017 13:24:45 GMT
Pragma: no-cache
Server: bfe/1.0.8.18
Set-Cookie: BDORZ=27315; max-age=86400; domain=.baidu.com; path=/

<!DOCTYPE html>
<html lang="en">
………………以下为网页html代码，略…………
</html>
````
### 2. http响应包含以下部分
- 1 协议/版本号 状态码 状态解释
2 Key1: value1
2 Key2: value2
2 Content-Length: 17931
2 Content-Type: text/html
3
4 要下载的内容
-  **其中需要注意的是**
第 2 部分中的 Content-Type 标注了第 4 部分的格式
第 2 部分中的 Content-Type 遵循 MIME 规范
第一部分的状态解释没什么用

### 3. 关于响应的状态码
- 1xx消息：代表请求已被接受，需要继续处理。这类响应是临时响应，只包含状态行和某些可选的响应头信息，并以空行结束。（不常用，大致知道即可）

- 2XX：表示成功
  - 200 OK：请求已成功，请求所希望的响应头或数据体将随此响应返回。
  - 204 No Content：表示请求已成功处理，但是没有内容返回，意思等同于请求执行成功，但是没有数据，浏览器不用刷新页面，也不用导向新的页面。
  - 206 Partial Content：服务器已经成功处理了部分GET请求。（客户端进行了范围请求） 响应报文中包含Content-Range指定范围的实体内容。
- 3XX：重定向
  - 301 Moved Permanently：表示访问的资源被永久移除，即请求的资源已经永久的搬到了其他位置 。
  - 302 Found ：表示所访问的资源临时被移除，以后还会回来，即临时重定向，表示请求的资源临时搬到了其他位置 。
  - 303 See Other ：表示请求资源存在另一个URI，应使用GET定向获取请求资源 。
  - 304 Not Modified ：表示资源未被修改，即客户端仍然具有以前下载的副本，不需要重新传输资源。
  - 307 Temporary Redirect：临时重定向，和302有着相同含义 ，尽管302标准禁止POST变为GET，但没人听他的 ，而307就会遵照标准，不会从POST变为GET 
但处理响应行为，各个浏览器可能不同。

- 4XX：客户端错误
  - 400 Bad Request：表示请求报文存在语法错误或参数错误，服务器不理解 ，服务器不应该重复提交这个请求 ，需要修改请求内容后再次发送。
  - 401 Unauthorized：表示发送的请求需要有HTTP认证信息或者是认证失败了 ，返回401的响应必须包含一个适用于被请求资源的WWW-Authenticate首部以质询用户信息 ，浏览器初次接受401时，会弹出认证窗口。
  - 403 Forbidden ：表示对请求资源的访问被服务器拒绝了 ，服务器可以对此作出解释，也可以不解释 ，想说明的话可以在响应实体的主体部分描述原因 。比如说你可能没有访问权限。
  - 404 Not Found：表示服务器找不到你请求的资源 ，但允许用户的后续请求。

- 5XX：服务器错误
  - 500 Internal Server Error：通用错误消息，服务器遇到了一个未曾预料的状况，导致了它无法完成对请求的处理。没有给出具体错误信息。
  - 502 Bad Gateway：作为网关或者代理工作的服务器尝试执行请求时，从上游服务器接收到无效的响应。
  - 503 Service Unavailable：表示服务器超负载或正停机维护，无法处理请求 ，如果服务器知道还需要多长时间，就写入Retry-After首部字段返回。

### 4. 使用Chrome浏览器查看http响应
1. 打开 Network
0. 输入网址
0. 选中第一个响应
0. 查看 Response Headers，点击「view source」
0. 你会看到响应的前两部分
0. 查看 Response 或者 Preview，你会看到响应的第 4 部分

举个例子：百度首页响应代码

![百度首页响应代码](https://upload-images.jianshu.io/upload_images/11827773-a69411105b833e6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 三、GET 和 POST 的区别是什么

这是一个很常见的面试题，我这里总结的答案是：

- **参数**。GET 的参数放在 url 的**查询参数里**，POST 的参数（数据）放在**请求消息体里**。
- **安全**。**GET 没有 POST 安全**（当然实际上二者都没有那么的安全）。因为GET请求很容易伪造（比如`<a>`、`<img>`、`<link>`、`<script>`、`<form>`标签都可以发送GET请求）。
- **数据长度**。GET 的参数（url查询参数）有长度限制，一般是 **1024 个字符**。POST 的参数（数据）**没有长度限制**（话虽这么说，但实际POST还是有4~10Mb 的限制，但是相对于GET已经大多了）
- **包**。GET 请求**只需要发一个包**，POST 请求需要发**两个以上包**（因为 POST 有消息体）（虽然GET 也可以用消息体但是不常用）
- **作用**。GET 用来**读数据**，POST 用来**写数据**，POST **不幂等**（幂等的意思就是不管发多少次请求，结果都一样。）
