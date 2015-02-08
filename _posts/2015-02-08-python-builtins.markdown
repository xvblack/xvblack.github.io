---
title: Python Builtins
layout: post
date: February 8, 2015 – 14:48
category: python

---

Python的builtins有很多有趣的东西。

1. iter

	`iter`是一个python的builtin function，用于将参数转化为iterator。其支持的用法有两种：

	- `iter(iterable)`：调用iterable的`__iter__`方法，获得一个iterator
	- `iter(callable, boundary)`：调用callable，知道callable返回了boundary的值，抛出`StopIteration`。

2. property

	`property`是一个很神奇的方法。如果用过ruby的话，会知道ruby处理对象成员的赋值和读取操作是直接定义了形如`attr_a=`和`attr_a`这样的函数，然后将对应的读取和赋值转化为函数调用。
	而在这个问题上，python则提供了另一种方法。对于任意一个继承了`object`的类，也就是python描述的new-style classes，我们可以通过如下的方法修改赋值和读取的操作：
		
		class A(object):
			def __init__(self, x):
			 	self._x = x
			def getx(self):
				return self._x
			def setx(self, x):
				self._x = x
			def delx(self, x):
				del self._x
			x = property(getx, setx, delx)

	这时任何对x的读取，赋值以及删除操作都会调用对应的函数，而不是直接生成一个名为x的attribute。而实际上，对于new-style classes的实例的任何一个形如obj.attr的调用，其本身都会有一个通过`__getattribute__`进行的查找（一些[特殊方法](https://docs.python.org/2/reference/datamodel.html#new-style-special-lookup)除外），这些之后再写。

（待续）