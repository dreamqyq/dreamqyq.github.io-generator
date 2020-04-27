---
title: "Java的包管理与Maven"
date: 2020-04-27
draft: false
tags: ["Java", "Maven"]
categories: ["Java"] 
---

**前言**：在Java的世界中，【包】是最基本的结构，因此包管理就是Java项目中的一件特别重要的事情。本文介绍的就是Java世界中的包管理，以及最流行的包管理工具Maven。

---

## Java的包管理
要介绍Java的包管理，我们就要先知道什么是包：
### 包管理中的【包】指的是什么？
- 首先，我要知道JVM（Java Virtual Machine，即Java虚拟机）的工作原理。实际上，JVM的工作被设计的相当简单：
  1. 执行一个类的字节码
  0. 假如这个过程中碰到了新的类，就加载它，然后执行第一个步骤
- 那么，我们从哪儿加载这些类呢？
答案是【class path】
在IDEA中，我们执行了一段程序，在界面的下方，我们没有注意到的一段灰色的代码，里面就藏着classpath
![idea界面](https://upload-images.jianshu.io/upload_images/11827773-f25df598443fcdff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在这段命令中，-classpath 或者 -cp后面接的就是这段程序找包的地方。

- 类的全限定类名（目录层级）唯一确定了一个类
- 包，就是把许多类放在一起打的压缩包

### 包的传递性依赖
- 何为传递性依赖，即：你依赖的类还依赖了别的类
- 于是，恐怖的事情发生了：**classpath hell**（依赖地狱）
  - 全限定类名是类的唯一标识
  - 如果，我引用了A包和B包，B包中又引用了A包，就会出现多个同名类（A包）出现在classpath中，这就是下文即将说到的【**包冲突**】
  - 如果出现了包冲突，默认的解法就是：**优先使用classpath排在前面的包**。
- pom.xml （pom，就是Project Object Model）就是一份项目的说明书，看了pom.xml就知道这个项目是如何工作的。   

### 什么是包管理
简单来说包管理的本质就是下面三点：
* 你要使用一些第三方类，总要告诉JVM从哪里找吧？
* 包管理的本质就是告诉JVM如何找到所需的第三方类库
* 以及成功的解决其中的冲突问题

## Maven的包管理
>**Apache Maven**，是一个软件（特别是[Java](https://zh.wikipedia.org/wiki/Java_(%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80) "Java (编程语言)")软件）[项目管理](https://zh.wikipedia.org/wiki/%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86 "项目管理")及[自动构建](https://zh.wikipedia.org/wiki/%E8%87%AA%E5%8A%A8%E6%9E%84%E5%BB%BA "自动构建")工具，由[Apache软件基金会](https://zh.wikipedia.org/wiki/Apache%E8%BD%AF%E4%BB%B6%E5%9F%BA%E9%87%91%E4%BC%9A "Apache软件基金会")所提供。基于项目对象模型（缩写：POM）概念，Maven利用一个中央信息片断能管理一个项目的构建、报告和文档等步骤。

Maven是一个划时代的成就，必须强调，Maven远远不止是包管理工具，还是一个自动化构建工具。Java在经历了很多年的发展后，Maven的应运而生，是我们在庞大的Java项目中管理包不再是一件令人头疼的事情。

### Maven —— 划时代的包管理
- Maven的中央仓库（即远程仓库），按照一定的约定存储包，我们可以在中央仓库中，找到所有的已经发布了的包，用于我们查找及下载。
- Maven的本地仓库，默认位于`~/.m2`，下载的第三方包放在这里缓存
- 而Maven最优秀的地方就是，它规定了每一个包的命名规范，按照这种约定，我们就能方便的在中央仓库找到不同团队、不同版本的包，在项目中，也可以确定的引入我们想要引入的包。而Maven的命名规范主要是按照以下三个维度：
  - groupId / artifactId / version
> `groupid`和`artifactId`被统称为“坐标”是为了保证项目唯一性而提出的，如果你要把你项目弄到maven本地仓库去，你想要找到你的项目就必须根据这两个id去查找。

> `groupId`一般分为多个段，这里我只说两段，第一段为域，第二段为公司名称。域又分为org、com、cn等等许多，其中org为非营利组织，com为商业组织。举个apache公司的tomcat项目例子：这个项目的groupId是org.apache，它的域是org（因为tomcat是非营利项目），公司名称是apache，artifactId是tomcat。

> 比如我创建一个项目，我一般会将`groupId`设置为`cn.enoch`，cn表示域为中国，`enoch`是我的名字，`artifactId`设置为`testProj`，表示你这个项目的名称是`testProj`，依照这个设置，你的包结构最好是`cn.enoch.testPro`打头的，如果有个`StudentDao`，它的全路径就是`cn.enoch.testProj.dao.StudentDao`

### 拓展：[语义化版本]([https://semver.org/lang/zh-CN/)
5.0.0-M1 【milestone 里程碑】
5.0.0-RC1 【Release candiate，正式版本的候选版本】
alpha：内部版本
beta：公测版本
SNAPSHOT：快照版本，用于开发联调的包

## Maven 包冲突及解决

### 什么是包冲突
简单的来说就是因为__传递性依赖__导致某一个包被引入了两次，在classpath中出现了两次，从而导致我们在运行Java程序时，classpath中**后出现**的那次引入的包不会被引入到项目中来。
### 包冲突可能会出现的异常
* AbstractMethodError
* NoClassDefFoundError
* ClassNotFoundException
* LinkageError

### Maven传递性依赖的自动管理：
- 原则：绝不允许最终的classpath出现同名不同版本的jar包
- 依赖冲突的解决原则：**最近的胜出**
![远，还是近？](https://upload-images.jianshu.io/upload_images/11827773-599dca2e9293cc9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上图中，最终形成的classpath，【C0.2】会被舍弃，即，包冲突导致我们被迫使用了低版本的jar包

### 怎么解决呢？
- 首先：需要理清项目中的依赖关系，找到哪一个依赖因为maven自动给舍弃导致的问题
- 然后可能需要去看一下这个包的不同版本的源码，找到为什么maven自动舍弃一些包依赖后会报异常（比如上面的例子：高版本被舍弃，但是我们用到了高版本才有的方法）
- 解决它（两种解法）
  1. 在我的项目中，直接依赖【C0.2】版本，这样maven就会选择最近的【C0.21】，即在项目pom中直接引入该版本包的dependency。
  2. 排除掉【D包】的传递性依赖，这样我们的依赖树就只有一个【C0.2】包了 。使用exclusions，排除掉D包依赖的C包。
![exclusions的用法](https://upload-images.jianshu.io/upload_images/11827773-aaed2ea8bb018c28.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 如果两个发生冲突的包”距离“一样呢？
![距离一样，怎么办](https://upload-images.jianshu.io/upload_images/11827773-4d9bd65bcf56ca1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这张图，【C0.1】和【C0.2】具体相同，那么maven为了保证classpath同名包只有一个，就会选择最先声明的包，舍弃后者（比如先依赖的A，就会选择C0.1）

## Maven的其他知识 

### scope

- 依赖的scope（指定依赖只在scope中有效），实现依赖的隔离 
  - compile/test/provided等等
  - main 生产代码 / test 测试代码 
- 最重要的有三个：(test / compile / provided)
  - `<scope>test</scope>`
  只有在测试代码中（@TEST）才看得到这个包，在main里面无法使用这个包
  - `<scope>compile</scope>`
  在main和test都可见，即编译时候可见，运行时候可见，测试代码可见，生产代码可见
  - `<scope>provided</scope>`
  只在编译的时候有效，运行的时候就无效了，即只把这个包用于编译。
    - 编译：把源代码变成字节码的过程
    - 运行：jvm加载字节码并开始运行的过程
    - NoClassDefFoundError
    - tomcat
      比如你的代码经过编译以后要部署到tomcat容器中，tomcat容器会帮你添加一些第三方依赖包，为了防止包冲突，我们就需要把这些包设置成provided

### Maven查看依赖树
- `mvn dependency:tree`
展示的是解决冲突后的依赖树
- 在idea中看
- 安装maven helper插件
  pom文件中，选择dependency analyzer --》all dependencies as tree

（完）

