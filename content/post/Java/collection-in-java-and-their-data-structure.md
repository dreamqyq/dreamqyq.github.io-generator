---
title: "Collection体系的常用类及其背后的数据结构"
date: 2020-04-28
draft: false
tags: ["Java"]
categories: ["Java"] 
---

**前言**：Collection是最基本的集合接口，在JDK 1.2版本被引入到Java的世界中来。Collection的出现，使得Java拥有了前所未有的强大能力。本文就将介绍Collection体系下常用的类的实现以及它们背后的数据结构。

---

## Collection体系简介
![Java集合框架](https://upload-images.jianshu.io/upload_images/11827773-f46900007cf13fd6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> **Java集合框架**(**Java collections framework**)是一个包含一系列实作可重复使用集合的数据结构的类别 "类别 (计算机科学)")和界面"界面 (程式设计)")集合。 虽然称为“框架”，其使用方式却像个函式库。集合框架提供了定义各式各样集合的界面和实作上述集合的类别。

我们可以在IDEA中搜到Collection.java，然后发现在注释中介绍Collection的第一段话是这么说的：
>  The root interface in the collection hierarchy.  A collection
 represents a group of objects, known as its elements.  Some collections allow duplicate elements and others do not.  Some are ordered and others unordered.  The JDK does not provide any direct implementations of this interface: it provides implementations of more specific subinterfaces like `Set` and `List`.  This interface is typically used to pass collections around and manipulate them where maximum generality is desired.

翻译过来是这样的：
`Collection`是Java Collection继承体系中的根接口。一个Collection代表一组对象，被称为它的元素。有一些集合**允许重复**的元素（如`List`），而另一些则**不允许重复**（如`Set`）。一些是**有序**的（如`ArrayList`），而有些则是**无序**的（如`HashSet`）。 JDK不提供对Collection这个接口的任何直接实现：它提供了很多对它子接口（如`Set`和`List`）的实现。该接口通常用于在非常通用的地方把Collection当作参数传来传去，同时对它们进行操作。

### 与数组的不同处
集合和数组在可被视作为一个团体上有着功能上的相似处。集合和数组其中一点不同的是，集合在声明时**不需要指定固定的容量**。集合可以在新增或移除内容时**自动地**增加或缩减其容量。 另外，集合**无法收纳基本数据类型**，像是整数（`int`)、长整数（`long`）或者双精度浮点数（`double`）。取而代之的是，集合可以收纳上述基本数据类型的**封装类型**（`Integer`、`Long`、`Double`）

## Collection体系下的三种结构
（本节的引用摘自jdk官方注释。）
### List（有序列表）
> An ordered collection (also known as a sequence).  The user of this interface has precise control over where in the list each element is inserted.  The user can access elements by their integer index (position in the list), and search for elements in the list. 
有序集合（也称为序列）。该界面的用户可以精确控制列表中每个元素的插入位置。用户可以通过其整数索引（列表中的位置）访问元素，并在列表中搜索元素。

List接口是一个有序的 Collection，使用此接口能够精确的控制每个元素插入的位置，能够通过索引(元素在List中位置，类似于数组的下标)来访问List中的元素，第一个元素的索引为 0，而且允许有相同的元素。List 接口存储一组**不唯一，有序**（插入顺序）的对象。

常用的List的实现类：`ArrayList`。

### Set （集合）
> A collection that contains no duplicate elements.  More formally, sets contain no pair of elements `e1` and `e2` such that `e1.equals(e2)`, and at most one null element.  As implied by its name, this interface models the mathematical set abstraction.
一个不包含重复元素的集合。更正式地说，集合不包含元素对e1和e2，使得`e1.equals(e2)`最多包含一个空元素。顾名思义，此接口对数学集合抽象进行建模。

Set 具有与 Collection 完全一样的接口，只是行为上不同，Set 不保存重复的元素。Set 接口存储一组**唯一，无序**的对象。

常用的Set的实现类：`HashSet`

需要注意的是：Set判断重复是通过`equals`方法，

### Map（映射表）
> An object that maps keys to values.  A map cannot contain duplicate keys; each key can map to at most one value. This interface takes the place of the Dictionary class, which was a totally abstract class rather than an interface.
将键映射到值的对象。映射不能包含重复的键；每个键最多可以映射到一个值。该接口代替了Dictionary类，后者是一个完全抽象的类，而不是一个接口。

Map 接口存储一组键值对象，提供key（键）到value（值）的映射。

常用的Map实现类：`HashMap`

## Collection体系常用实现类详解

### List
#### ArrayList（线性结构，基于动态数组）
- 本质上，`ArrayList`就是一个数组。
- 动态扩容的实现：
  - 创建一个更大的空间。然后把原先的所有元素拷贝过去
##### 线性结构
简单地说，线性结构就是表中各个结点具有线性关系。如果从数据结构的语言来描述，线性结构应该包括如下几点：
1、线性结构是非空集。
2、线性结构有且仅有一个开始结点和一个终端结点。
3、线性结构所有结点都最多只有一个直接前趋结点和一个直接后继结点。
线性表就是典型的线性结构，还有栈、队列和串等都属于线性结构。

#### LinkedList（链表）
##### 链表
[链表](https://baike.baidu.com/item/%E9%93%BE%E8%A1%A8/9794473)是一种数据元素按照链式存储结构进行存储的数据结构，这种存储结构具有在物理上存在非连续的特点。链表由一系列数据结点构成，每个数据结点包括数据域和指针域两部分。其中，指针域保存了数据结构中下一个元素存放的地址。链表结构中数据元素的逻辑顺序是通过链表中的指针链接次序来实现的。

#### ArrayList和LinkedList比较
由于二者是基于不同的数据结构实现的，基于链表和线性结构的不同，二者主要的不同用法：
- ArrayList 对于【查】、【改】操作性能更高
- LinkedList 对与【增】、【删】操作性能更高

### Set
#### HashSet
最常用，最高效的Set实现
- List可以使用Set来去重![Set去重](https://upload-images.jianshu.io/upload_images/11827773-412896773f3036dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**注意**：HashSet是无序的，如果需要有序的HashSet可以使用`LinkedHashSet`。

##### 散列表（Hash）
散列表源自于[散列函数](https://baike.baidu.com/item/%E6%95%A3%E5%88%97%E5%87%BD%E6%95%B0/2366288)(Hash function)，其思想是如果在结构中存在关键字和T相等的记录，那么必定在F(T)的存储位置可以找到该记录，这样就可以不用进行比较操作而直接取得所查记录。

#### TreeSet
TreeSet是有序的，因为Comparable的默认顺序是从小到大，所以TreeSet的默认顺序是从小到大，即自然顺序。
因此：TreeSet最大的用途就是用来排序的。TreeSet内部的数据结构是__红黑树__。

##### 树
[树](https://baike.baidu.com/item/%E6%A0%91/2699484)是典型的非线性结构，它是包括，2个结点的有穷集合K。在树结构中，有且仅有一个根结点，该结点没有前驱结点。在树结构中的其他结点都有且仅有一个前驱结点，而且可以有两个后继结点，m≥0。

#### LinkedHashSet（有顺序的HashSet）
保证内部顺序和插入的顺序一样。

### Map
#### HashMap
##### HashMap的线程不安全性
**注意**：HashMap是**线程不安全**的，在jdk的官方注释中也提到了【Note that this implementation is not synchronized.】。即：请注意，HashMap的实现没有被同步。

在多线程中，HashMap的死循环问题：在多线程中，扩容的时候，即resize的时候，有可能变成一个死循环的链表。感兴趣的可以移步：[疫苗：JAVA HASHMAP的死循环]([https://coolshell.cn/articles/9606.html](https://coolshell.cn/articles/9606.html)
)。因此在多线程中，应该使用ConcurrentHashMap，即并发的HashMap。

##### HashMap在jdk1.7后的改变：同一个哈希桶中存储的数据结构由链表改为红黑树
如果恰好一个Map的所有的key的hashCode都是一样的，那么他们就会放到同一个哈希桶中，那么这个HashMap就会变成一个效率极低的链表，导致程序运行变慢，丧失了使用Hash算法的好处。JDK7以后，发生哈希碰撞，存在同一个哈希桶中的数据不再是一个链表，而是变成了一个红黑树，从而提高了性能。

#### TreeMap
内部数据结构同TreeSet，此处不再赘述。
HashMap和HashSet本质上是⼀种东⻄：
- HashMap的key的set就是一个HashSet
- HashSet的实现中就包含了一个HashMap

## 哈希算法简介
### Java世界里第二重要的约定（hashCode）
（第一重要的约定是equals约定）
- 同于一个对象必须始终返回相同的hashCode
- 两个对象的equals返回true，则必须返回相同的hashCode
  - 因此，当我们重写equals方法时，必须重写hashCode方法
- 两个对象不等，也可能返回相同的hashCode

哈希就是一个单项的映射，举例来说就像百家姓：先把所有人按照姓来分类，然后再查找：人名怎么取hashCode呢，通过取他的姓；那对象Object就会通过hashCode算法，映射成一个int。

在内存中，上面百家姓的例子，姓就是内存中的哈希桶，通过hashCode方法算出一个哈希值，然后放到哈希桶中。如果我们有10万条数据，通过哈希算法分散到10万个哈希桶中，那么我们查找某一条数据，只需要查找一次，因此通过哈希算法，查找的时间复杂度是呈指数下降的。

## Collection的其他实现
### Queue（队列）
有优先级的集合，LILO（Last In Last Out）
#### 队列
[队列](https://baike.baidu.com/item/%E9%98%9F%E5%88%97/14580481)和栈类似，也是一种特殊的线性表。和栈不同的是，队列只允许在表的一端进行插入操作，而在另一端进行删除操作。一般来说，进行插入操作的一端称为队尾，进行删除操作的一端称为队头。队列中没有元素时，称为空队列。

### Deque（双端队列）
允许在两端进行增删元素。
### ~~Veetor~~ 
Vector是ArrayList的前身，已弃用
### ~~Stack~~（栈）
LIFO（Last In First Out），如果需要”栈“这种数据结构，推荐使用`Deque`
#### 栈
[栈](https://baike.baidu.com/item/%E6%A0%88/12808149)是一种特殊的[线性表](https://baike.baidu.com/item/%E7%BA%BF%E6%80%A7%E8%A1%A8/3228081)，它只能在一个表的一个固定端进行数据结点的插入和删除操作。栈按照后进先出的原则来存储数据，也就是说，先插入的数据将被压入栈底，最后插入的数据在栈顶，读出数据时，从栈顶开始逐个读出。栈在汇编语言程序中，经常用于重要数据的现场保护。栈中没有数据时，称为空栈。

### ConcurrentHashMap
线程安全的HashMap
### PriorityQueue（使用”堆“来实现的优先级队列）
#### 堆
[堆](https://baike.baidu.com/item/%E5%A0%86/20606834)是一种特殊的树形数据结构，一般讨论的堆都是二叉堆。堆的特点是根结点的值是所有结点中最小的或者最大的，并且根结点的两个子树也是一个堆结构
