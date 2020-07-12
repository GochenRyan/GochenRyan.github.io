title: 【Python源码剖析】编译Python
id: 15
date: 2020-07-12 22:42:01
tags:
    - Python
categories: Python源码剖析
---
## 写在前面
先推一下大阳哥的[这篇文章](https://lmikoto.com/2020/07/12/%E4%BD%BF%E7%94%A8Notion%E5%81%9A%E5%8D%9A%E5%AE%A2%E7%9A%84%E5%90%8E%E5%8F%B0/)。notion！记笔记的神！说说这篇文章吧，刚开始看《Python源码剖析》，这系列（如果不鸽的话）文章算是笔记与扩展吧。与书中所用python2.5不同，我用的是python3.8(害，主要是不想看python2😑)。玩了一天游戏，写完这篇文章也算是有个心里安慰😋。

<!-- more -->

## 问题导读

- [ ]  Python总体架构是怎么样的？
- [ ]  python源代码是怎么组织的？
- [ ]  如何修改python的源码？

## Python总体架构

![Python%20150dc23d166e4cb4be1df963bb6482ee/python.png](/img/Python/python.png)

- File Groups
    - 核心模块
    - 库
    - 用户自定义模块
- Runtime Environment
    - 对象/类型系统
    - 内存分配器
    - 运行时状态信息
- 解释器
    - Scanner
    - Parser
    - Compiler
    - Code Evaluator

## Python源码组织

![Python%20150dc23d166e4cb4be1df963bb6482ee/Untitled.png](/img/Python/Untitled.png)

- **Include 目录**：包含了 Python 提供的所有头文件，如果用户需要自己用 C 或 C++来编写自定义模块扩展 Python，那么就需要用到这里提供的头文件
- **Lib 目录**：包含了 Python 自带的所有标准库，且都是用 Python 语言编写的
- **Modules 目录**：包含了所有用 C 语言编写的模块，比如 math、hashlib 等。它们都是那些对速度要求非常严格的模块。而相比而言，Lib 目录下则是存放一些对速度没有太严格要求的模块，比如 os
- **Parser 目录**：包含了 Python 解释器中的 Scanner 和 Parser 部分，即对 Python 源代码进行词法分析和语法分析的部分。除此以外，此目录还包含了一些有用的工具，这些工具能够根据 Python 语言的语法自动生成 Python 语言的词法和语法分析器，与 YACC 非常类似
- **Objects 目录**：包含了所有 Python 的内建对象，包括整数、list、dict 等。同时，该目录还包括了 Python 在运行时需要的所有的内部使用对象的实现
- **Python 目录**：包含了 Python 解释器中的 Compiler 和执行引擎部分，是 Python 运行的核心所在
- **PCbuild 目录**：包含了 VS 的工程文件，研究 Python 源代码就从这里开始（这里采用 VS 2019 对 Python 进行编译）
- **Programs 目录**：包含了 Python 二进制可执行文件的源码

## Python源码编译

下载tarball源码包

[https://www.python.org/downloads/release/python-383/](https://www.python.org/downloads/release/python-383/)

![Python%20150dc23d166e4cb4be1df963bb6482ee/Untitled1.png](/img/Python/Untitled1.png)

编译

![Python%20150dc23d166e4cb4be1df963bb6482ee/Untitled2.png](/img/Python/Untitled2.png)

![Python%20150dc23d166e4cb4be1df963bb6482ee/Untitled3.png](/img/Python/Untitled3.png)

![Python%20150dc23d166e4cb4be1df963bb6482ee/Untitled4.png](/img/Python/Untitled4.png)

![Python%20150dc23d166e4cb4be1df963bb6482ee/Untitled5.png](/img/Python/Untitled5.png)

## 修改Python源码

Python的C API提供了一个输出对象的接口：

![Python%20150dc23d166e4cb4be1df963bb6482ee/Untitled6.png](/img/Python/Untitled6.png)

```cpp
PyAPI_FUNC(int) PyObject_Print(PyObject *, FILE *, int);
```

python2中，整数有两种类型，一种是系统支持范围内的整数，为int型，一种是变长的long类型，而python3中统一为long类型，因此修改longobject.c中的long_to_decimal_string函数：

![Python%20150dc23d166e4cb4be1df963bb6482ee/Untitled7.png](/img/Python/Untitled7.png)

```cpp
static PyObject *
long_to_decimal_string(PyObject *aa)
{
    PyObject *str = PyUnicode_FromString("I am always before long");
    PyObject_Print(str, stdout, 0);
    printf("\n");

    PyObject *v;
    if (long_to_decimal_string_internal(aa, &v, NULL, NULL, NULL) == -1)
        return NULL;
    return v;
}
```

对Python重新编译，运行编译后的python，输入print语句：

```python
>>> print(100)
'I am always before long'
100
```
