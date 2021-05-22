title: metaclass
id: 16
date: 2020-07-18 20:27:28
tags:
    - python
categories: Python
---
分析了metaclass的原理，并使用其来实现单例。

<!-- more -->


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