---
title: 【游戏客户端】MVVM
date: 2020-05-03 01:17:41
tags: 
    - 设计模式
    - 游戏客户端
---

## 写在前面

休息日LOL的时候，试着和枫哥解释一些开发涉及到的概念和设计。解释的时候，明明在说自己天天用的东西，却感觉如鲠在喉。出大问题！正好从事游戏客户端开发也快一年，积累了一些经验。期望能通过写博客，来整理、总结、备忘、**更新**学到的知识。本人水平有限，若有写的不对或者不严谨的地方，欢迎交流研讨。

<!-- more -->

## MVC、MVP、MVVM

网上已经解释的很详细了，这里不过多展开。从左到右依次是MVC、MVP、MVVM的图示（展示的为较常见的设计，仅做对比，不同的应用场景有不同的变种）：

![mvvm_1.png](/img/mvvm/compare.png)

### MVC

1. View 传送指令到 Controller；
2. Controller 完成业务逻辑后，要求 Model 改变状态；
3. Model 将新的数据发送到 View，用户得到反馈。

## MVP

1. 各部分之间的通信，都是双向的；
2. View 与 Model 不发生联系，都通过 Presenter 传递；
3. View 不部署任何业务逻辑，称为"被动视图"（Passive View），即没有任何主动性，而 Presenter非常厚，所有逻辑都部署在那里。

## MVVM

和MVP唯一的区别是，它采用**双向绑定**（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。

## 项目中的MVVM

MVVM适合游戏客户端UI界面的开发，是界面**组件化开发**的最佳实践。

* **viewmodel**负责为ui绑定数据容器、回调函数

* **ctrl**负责业务逻辑

* **data**负责数据缓存

* **c2s**、**s2c**负责CS的数据交互

* **\_\_init\_\_** 提供对外的接口

什么，你问我**view**在哪？当然是UI编辑器自动生成布局文件+公共代码搞定😀

大概的设计如图：


![mvvm_2.png](/img/mvvm/mymvvm.png)

开发一个较简单的界面时，可能只有viewmodel、ctrl、\_\_int\_\_三个文件，因为复用了**数据更新框架**（负责维护客户端常用基本数据的一个更新框架）的data和通用的**tools组件**（可复用的viewmodel组件）。



参考资料：

[https://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html](https://www.ruanyifeng.com/blog/2015/02/mvcmvp_mvvm.html)

[https://juejin.im/post/5b3a3a44f265da630e27a7e6](https://juejin.im/post/5b3a3a44f265da630e27a7e6)


