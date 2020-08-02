title: metaclass
id: 16
date: 2020-07-18 20:27:28
tags:
    - python
categories: Python
---
## 写在前面
最近看《麦田里的守望者》的时候，看到里面一句话，顿了很久才回过神。它是这么说的：记住该记住的，忘记该忘记的。改变能改变的，接收不能接受的。在未阅读这篇文章前，我在朋友圈也发过类似的文字（仅自己可见）。

<!-- more -->

- “记住该记住的”：从什么时候开始，开始习惯在朋友圈记录的呢？大概是心情郁闷、纠结的时候，会写下一些看似有道理的话。不过时隔多日又能看见类似的文字，人生到底是个环😵；
- “忘记该忘记的”：有些事，时间长了，自然便忘记了。只要多睡几次好觉，坏情绪、坏思想、坏回忆在某次醒来，突然就没有了，像可控性失忆一样；
- “改变能改变的”：改变自己已经很难了，改变他人更是不讲道理。不要为难别人，这也是为难自己😑，好好改变自己就行；
- “接收不能接受的”：有些事情，意识到了，却不愿意接受，这是最折磨人的，能持续很久。你把自己沉到其他事情里去，然后发现，woc我把所有事情都搞砸了。人生的智慧在于放下，大师，我悟了。

又想起一句话：道理我都知道，但是我就是做不到。没事，我带你看看python中的元类，看看它是怎么做到的🤣。

## 问题导读
- [ ]  \_\_new\_\_、\_\_init\_\_、\_\_call\_\_有啥联系与区别？
- [ ]  元类是什么？
- [ ]  原理是什么？
- [ ]  怎么使用？
- [ ]  怎么用metaclass实现单例？

## \_\_new\_\_、\_\_init\_\_、\_\_call\_\_
先了解一下这几个前置知识点，后面会用到。
- **\_\_new\_\_**: 用于创建对象并返回对象，当返回对象时会自动调用__init__方法进行初始化
- **\_\_init\_\_**: 在对象创建好之后初始化变量
- **\_\_call\_\_**: 
    - 只要一个对象对应的class对象中实现了“\_\_call\_\_”操作，那么这个对象就是一个可调用的对象
    - 所谓调用，就是执行对象的type所对应的class对象的tp_call操作
    - 类也是对象，元类（metaclass）是用来创建类（对象）的可调用对象，在元类里重写__call__，就可以在实例化对象的时候搞事情

## 元类

元类（metaclass）是用来创建类的可调用对象。对于实例对象、类和元类，可以用下面的图来描述:  
![metaclass1](/img/Python/metaclass1.png)

那么，元类有什么用呢？看看下面的例子。  
- 在 Python2 中，我们只需在 Foo 中加一个\_\_metaclass\_\_的属性:
```python
class CPrefixMetaClass(type):
	def __new__(cls, name, bases, attrs):
		_attrs = (("my_" + name, value) for name, value in attrs.items())
		_attrs = dict((name, value) for name, value in _attrs)
		return type.__new__(cls, name, bases, _attrs)

class CTest(object):
	__metaclass__ = CPrefixMetaClass
	name = "test"

test = CTest()
print test.my_name
```
- 在 Python3 中，这样做:
```python
class CPrefixMetaClass(type):
	def __new__(cls, name, bases, attrs):
		_attrs = (("my_" + name, value) for name, value in attrs.items())
		_attrs = dict((name, value) for name, value in _attrs)
		return type.__new__(cls, name, bases, _attrs)

class CTest(metaclass=CPrefixMetaClass):
	name = "test"

test = CTest()
print(test.my_name)
```
结果：
```python
F:\PyProject\untitled\venv\Scripts\python.exe F:/PyProject/untitled/metaclass.py
test
```
CPrefixMetaClass继承type，这是因为CPrefixMetaClass是用来创建类的\_\_new\_\_ 是在 \_\_init\_\_ 之前被调用的特殊方法，它用来创建对象并返回创建后的对象，对它的参数解释如下：
- cls：当前准备创建的类
- name：类的名字
- bases：类的父类集合
- attrs：类的属性和方法，是一个字典

可以直接使用type来动态创建类，不过在项目中没怎么用到，这里提及一下。感兴趣的可以搜一下，很简单。
## 使用元类来创建单例
元类定义__call__方法，可以抢在类运行 \_\_new\_\_ 和 \_\_init\_\_ 之前执行，也就是创建单例模式的前提，在类实例化前拦截掉。type的__call__实际上是调用了type的__new__和__init__。  
![metaclass2](/img/Python/metaclass2.png)  
### 单例的实现代码  
singleton.py
```python
# -*- coding: utf-8 -*-
g_lSingletonCls = []

def Reset():
	global g_lSingletonCls
	for oCls in g_lSingletonCls:
		oCls.ClearInstance()


class Singleton(type):
	def __new__(cls, *args, **kwarg):
		print "Singleton.__new__"
		return type.__new__(cls, *args, **kwarg)

	def __init__(cls, name, bases, dct):
		print "Singleton.__init__"
		super(Singleton, cls).__init__(name, bases, dct)
		cls.instance = None

	def __call__(cls, *args, **kwargs):
		print "Singleton.__call__"
		if cls.instance is None:
			cls.instance = super(Singleton, cls).__call__(*args, **kwargs)
			global g_lSingletonCls
			g_lSingletonCls.append(cls)
			return cls.instance

	def ClearInstance(cls):
		print "Singleton.ClearInstance"
		cls.instance = None

	def HasInstance(cls):
		return cls.instance != None


class CCase(object):
	__metaclass__ = Singleton
	def __init__(self):
		print "CCase.__int__"

	def __new__(cls, *args, **kwargs):
		print "CCase.__new__"
		return object.__new__(cls)
```
test_singleton.py
```python
#-*- coding:utf-8

import singleton

oCase = singleton.CCase()
print "before\n", singleton.CCase.HasInstance()

singleton.CCase.ClearInstance()
print "after\n", singleton.CCase.HasInstance()
```
### 结果：  
![metaclass3](/img/Python/metaclass3.png)

## 参考链接
[https://wiki.jikexueyuan.com/project/explore-python/Class/metaclass.html](https://wiki.jikexueyuan.com/project/explore-python/Class/metaclass.html)  
[https://zhuanlan.zhihu.com/p/98440398](https://zhuanlan.zhihu.com/p/98440398)

## 写在最后
想要知道更多底层的细节，建议阅读《Python源码剖析》第十二章，那里有你想要的答案。