---
title: "使用Hugo搭建个人博客网站"
date: 2020-04-12T19:41:28+08:00
draft: false
tags: ["博客"]
categories: ["Linux"] 
---

## 前言
两年前用Hexo搭建博客，但是因为各种配置问题没有继续跟进，之后就一直在[简书](https://www.jianshu.com/u/150c4ef48860)写的博客。恰逢看到新的博客生成工具[Hugo](https://gohugo.io/)，所以重新搭建了一个个人博客网站，通过Github Pages，域名重定向来实现线上访问。本文介绍的就是如何使用Hugo搭建个人博客网站的过程。

## Hugo简介
不同与Hexo是用JavaScript实现的，Hugo是由Go语言实现的静态网站生成器。简单、易用、高效、易扩展、快速部署。从我的个人体验来讲，Hugo比Hexo速度更快，而且不用依赖一大堆东西，一个二进制文件就可以搞定。
> The world’s fastest framework for building websites. Hugo is one of the most popular open-source static site generators. With its amazing speed and flexibility, Hugo makes building websites fun again.

这是[Hugo](https://gohugo.io/)的主页对自己的介绍，看这嚣张的简介，__“全球最快的网站建设框架”__，就不禁想让人来体验一把。

那么就开始吧！

## Hugo快速开始
[Hugo](https://gohugo.io/)的首页有一个显眼的按钮【Quick Start】，点击它，按步骤完成即可
![Hugo首页](/images/Hugo.png)

### Hugo安装

#### Mac
```
brew install hugo
hugo version
```
#### Windows
去[Hugo releases页面](https://github.com/gohugoio/hugo/releases)下载`hugo_xxx_Windows-64bit.zip` 将它解压到一个安全的目录，然后把这个目录添加到环境变量PATH中，重启终端，运行`hugo version`，正确展示了版本号就说明安装成功。

### 快速搭建博客
可以进入[Hugo](https://gohugo.io/)首页，点击Quick Start快速开始，从Step 2开始抄代码，直到Step 7结束。（Step 1是安装Hugo，我们在上文已经介绍了。）

也可以根据接下来的步骤依次执行：

1. 创建一个新的项目（quickstart 是项目名称，可以改成你想要的名字）
    ```
    hugo new site quickstart
    ```
0. 添加一个主题（这里会安装一个默认主题ananke）
    ```
    cd quickstart
    git init
    git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke

    echo 'theme = "ananke"' >> config.toml
    ```
0. 创建一个新的博客
    ```
    hugo new posts/my-first-post.md
    ```
    注意：博客文件开头的 `draft: false`要改成true，否则改博客不会被生成到public目录中。
0. 开启 Hugo Serve，用于本地预览
    ```
    hugo server -D
    ```
0. 定制主题，如果对步骤2安装的默认主题不满意，可以自己选择一个主题进行替换
    - 重复步骤2，把你想要替换的主题GitHub路径替换一下就好，然后在配置文件`config.toml`里修改下面的配置
    ```
    baseURL = "https://example.org/"
    languageCode = "en-us"
    title = "My New Hugo Site"
    theme = "ananke"
    ```
    - baseURL 改成你的博客主页地址
    - languageCode 如果是中文博客，可以改成`zh-CN`
    - title 即博客名称
    - __theme__ 这里是重点，把theme的值改成你替换的主题的名称，然后主题就生效啦。
0. 构建静态页面
    ```
    hugo -D
    ```
    这个命令执行以后会生成一个`./public`的目录，将这个目录上传到GitHub，创建一个名为`dreamqyq.github.io`的项目（dreamqyq 这里替换成你的GitHub账号名），然后使用GitHub Pages就可以让别人来看你的博客啦。

### Hugo常用命令
- `hugo` 构建静态文件
- `hugo server -D` 本地预览
- `hugo new blogName.md` 生成一个新的博客


## 主题
我的个人博客网站的主题使用了[maupassant](https://github.com/flysnow-org/maupassant-hugo) 主题，在这个主题的Github主页README中有详细的配置方法，这里就不再赘述。如果喜欢的话可以参考教程进行配置。


## 遇到的问题
在配置教程的时候遇到了一个问题，上文中提到的配置`bashURL`应该是个人博客网站的地址，由于我做了Gihubub Pages域名转发，使用了我自己的域名 https://enochqin.xyz。在最开始配置的时候，写成了`https://www.enochqin.xyz`，导致博客的很多功能各种出错，后来才意识到，我的域名没有做www的映射，导致其实`https://www.enochqin.xyz`这个网址是不能访问的。

没有www和带www的域名是被看做两个不同的域名的。一般我们都会将两个域名设置指向相同的一个网站。但是实际上带www的和不带www的域名完全可以设置指向两个不同的服务器。这样你访问带www和不带www的地址，看到的是两个不同的网站了。


（完）
