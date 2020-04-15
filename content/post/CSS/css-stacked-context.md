---
title: "CSS堆叠上下文"
date: 2018-06-02
draft: false
categories: ["CSS"] 
tags: ["CSS"]
---

**前言**：设置了`z-index:9999;`的层叠顺序一定高于`z-index:0;`吗？很明显这句话在某些情况下是不成立的，而引起其不成立的原因就是堆叠上下文的现象。本文主要介绍关于css中堆叠上下文的概念、引起的原因等。
**注**：友情链接：关于堆叠上下文的[MDN介绍](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Understanding_z_index/The_stacking_context)，张鑫旭的博客[《深入理解CSS中的层叠上下文和层叠顺序》](http://www.zhangxinxu.com/wordpress/2016/01/understand-css-stacking-context-order-z-index/)

---

## 1、什么是堆叠顺序

从最底层到最高层的顺序如下：
1. 父级元素的background
2. 父级元素的border
3. 块级子元素
4. 浮动元素
5. 父元素的文字或内联子元素
6. 定位元素z-index: 0
7. 定位元素z-index: +

其中，**负的z-index**出现的位置有两种可能：
- 父元素是`position:static;`时：**负的z-index**出现在**0号位**（即1.父元素的background的底下一层）
- 因一些原因导致了层叠上下文现象出现时，**负的z-index**出现在**2号位与3号位的中间**，即只要出现了层叠上下文，子元素的`z-index`即使是负的，也会出现在父元素的上边。

![普通情况下的层叠顺序示意图](https://upload-images.jianshu.io/upload_images/11827773-0877f0025be66472.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 2、层叠上下文出现的原因

- 根元素 (HTML),
- z-index 值不为 "auto"的 绝对/相对定位，
- 一个 z-index 值不为 "auto"的 flex 项目 (flex item)，即：父元素 display: flex|inline-flex，
- opacity 属性值小于 1 的元素（参考 the specification for opacity），
- transform 属性值不为 "none"的元素，
- mix-blend-mode 属性值不为 "normal"的元素，
- filter值不为“none”的元素，
- perspective值不为“none”的元素，
- isolation 属性被设置为 "isolate"的元素，
- position: fixed
- 在 will-change 中指定了任意 CSS 属性，即便你没有直接指定这些属性的值（参考 这篇文章）
- -webkit-overflow-scrolling 属性被设置 "touch"的元素

## 3、我对层叠上下文现象的理解

假设有两个部门，分别是【甲】和【乙】，【甲】在前，【乙】在后，正常情况下，【甲】【乙】中的职员的地位由`z-index`决定，`z-index`相同的时候，【乙】中职员级别比【甲】高；而当出现了**层叠上下文**现象时，就相当甲、乙两个部门的负责的项目发生了变化，假如【甲】负责一个是千万级的项目，【乙】负责的是日常十万级别的项目，所以最终【甲】中的职员，哪怕在部门中的地位再低（即`z-index:0`），获得的薪酬也会比【乙】部门中的职员包括部门leader（即`z-index:9999`）要高。


## 4、一个例子
代码链接
http://js.jirengu.com/boyocekayo/1/edit
html结构
```html
<div class='parent'>
  <div class="a relative">a
    <div class="a1">a1</div>
  </div>
  <div class="b relative">b
    <div class="b1">b1</div>
  </div>
</div>
```
css样式
```css
.parent{
  height: 200px;
  border: 10px solid rgba(255,0,0, 1);
  padding: 15px;
  margin: 12px;
  background: #000;
}

.relative{
  width: 100px;
  height: 100px;
  background: orange;
  position: relative;
  border: 1px solid red;
  
}

.a1,.b1{
  background: green;
  height:30px;
  position: relative;
}
.a1{
  z-index:999;
    margin-top:0px;
}
.b1{
  background: blue;
  margin-top: -80px;
  z-index: 0;
}
/*下列代码导致层叠上下文现象*/
.a{
  position: relative;
  z-index: 1;
}
.b{
  position: relative;
  z-index: 1;
}


```
正常情况下，绿色的a1的`z-index`比蓝色的b1高，所以a1能盖住b1
![](https://upload-images.jianshu.io/upload_images/11827773-c2e7ab31cb9a0be8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

出现了层叠上下文后，即使a1的`z-index:9999;`,b1`z-index:0;`，蓝色的b1还是还是盖住了绿色的a1。
![](https://upload-images.jianshu.io/upload_images/11827773-228d6b05d4fcd639.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


