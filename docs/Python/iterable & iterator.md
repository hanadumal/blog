---
date created: 2023-03-03 13:24:17 +08:00
date updated: 2023-03-03 22:06:19 +08:00
title: iterable & iterator
category: Python
share: true
---
今天我们来探讨下Python中的可迭代对象(iterable)和迭代器(iterator)。每个Python程序员在日常编码中都会写下的大量的for循环，但对于紧密相关的这两个对象，有可能仍然会感到有所陌生。就让我们重新对其研究一番。先从中英文术语对照开始吧～

# glossary
object: 对象
iterable: 可迭代对象
iterator: 迭代器
iterator protocol: 迭代器协议
for-loop: for循环

# iterable
首次接触新概念的时候很容易被这几个名字弄晕，在意识中需要首先知道我们讨论的是对象，只是它具有可迭代的特征。接下来先明确下iterable的定义。按照[Python术语表](https://docs.python.org/3/glossary.html#term-iterable)中的解释：

An object capable of returning its members one at a time. Examples of iterables include all sequence types (such as [`list`](https://docs.python.org/3/library/stdtypes.html#list "list"), [`str`](https://docs.python.org/3/library/stdtypes.html#str "str"), and [`tuple`](https://docs.python.org/3/library/stdtypes.html#tuple "tuple")) and some non-sequence types like [`dict`](https://docs.python.org/3/library/stdtypes.html#dict "dict"), [file objects](https://docs.python.org/3/glossary.html#term-file-object), and objects of any classes you define with an `__iter__()` method or with a `__getitem__()` method that implements [sequence](https://docs.python.org/3/glossary.html#term-sequence) semantics.

所以iterable是一次返回一个元素的对象。那么iterable包含了哪些种类呢？
- 所有的内置sequence都是iterable
- 实现了sequence语义的对象都是iterable
- 一些非sequence的类型也是iterable,如dict和file(实现了`__getitem__()`)
- **实现__iter__()方法的自定义类**

Iterables can be used in a [`for`](https://docs.python.org/3/reference/compound_stmts.html#for) loop and in many other places where a sequence is needed ([`zip()`](https://docs.python.org/3/library/functions.html#zip "zip"), [`map()`](https://docs.python.org/3/library/functions.html#map "map"), …). When an iterable object is passed as an argument to the built-in function [`iter()`](https://docs.python.org/3/library/functions.html#iter "iter"), it returns an iterator for the object. This iterator is good for one pass over the set of values. When using iterables, it is usually not necessary to call [`iter()`](https://docs.python.org/3/library/functions.html#iter "iter") or deal with iterator objects yourself. The `for` statement does that automatically for you, creating a temporary unamed variable to hold the iterator for the duration of the loop. See also [iterator](https://docs.python.org/3/glossary.html#term-iterator), [sequence](https://docs.python.org/3/glossary.html#term-sequence), and [generator](https://docs.python.org/3/glossary.html#term-generator).

接下来是iterable的使用场景,它可以使用在:
- 用在for-loop中
- 用在需要sequence的地方（因为所有的sequence,都是iterable)
- 将iterable传递给iter()函数，来获取一个iterator。再使用iterator可以用来遍历一组值。
- 通常不需要自己额外调用iter()函数，或手动处理iterator，如for-loop中会自动调用并创建一个匿名的变量来保存iterator对象。

# iterator protocol
按照[标准库文档](https://docs.python.org/3/library/stdtypes.html#typeiter)所示下面两个方法组成了迭代器协议（iterator protocol)，实现了这两个方法的对象就是一个迭代器。
- `__iter__()` 方法返回iterator对象本身。这个函数是iterator能够跟for语句，in语句一起使用所必须的。
- `__next__()` 方法从iterator返回下一个元素。如果没有一个元素抛出StopIteration。

# iterator
根据wikipedia中的定义：在计算机编程中，迭代器是使程序员能够遍历容器（尤其是列表）的对象。

In [computer programming](https://en.wikipedia.org/wiki/Computer_programming "Computer programming"), an **iterator** is an [object](https://en.wikipedia.org/wiki/Object_(computing) "Object (computing)") that enables a programmer to traverse a [container](https://en.wikipedia.org/wiki/Container_(data_structure) "Container (data structure)"), particularly [lists](https://en.wikipedia.org/wiki/List_(abstract_data_type) "List (abstract data type)").

而如果我们非要考究Python本身对于Iterator的定义，查找久远的[PEP 234 - Iterators](https://peps.python.org/pep-3114/)中的内容：
	Classes can define how they are iterated over by defining an `__iter__()` method; this should take no additional arguments and return a valid iterator object. A class that wants to be an iterator should implement two methods: a `next()` method that behaves as described above, and an `__iter__()` method that returns `self`.

可知类使用__iter__()来定义怎么如何被迭代，这可以理解为iterable对象的特征。而要成为iterator需要实现的两个方法:next()和__iter__()。这和目前Python3中对于iterator Protocol的要求是一致的；只不过因为[PEP 3114](https://peps.python.org/pep-3114/) 的缘故next()函数被改名为`__next__()`。

# iter()
参照[built-in iter() function](https://docs.python.org/3/library/functions.html#iter)中的内容,iter()作为内建函数，用来创建iterator对象。
有两种调用方式
- iter(iterable)
	如果没有第二个参数，那么第一个参数要么必须是可迭代协议(iterable protocol)的对象(即必须有`__iter__()`方法)；或者第一个参数是支持序列化协议(sequence protocol)。如果参数类型不是这两种，会抛出TypeError。

- iter(callable, sentinel)
	如果有第二个哨兵参数(sentinel)，那么第一个参数必须是可调用对象(callable)。如果返回的值等于sentinel，会抛出StopIteration，否则就直接返回这个值。
	

至于用来生成iterator的函数名为什么叫iter()而不是iterator()？因为python有对内置函数使用缩写的传统呀，比如str() len() repr()

# StopIteration
StopIteration作为standard exception的其中之一，由Exception派生而来。从Python 2.2开始，在迭代结束后会抛出StopIteration。
如果迭代已经完成，但仍对迭代器继续调用next()，也还是抛出StopIteration，这种行为被称为"sticky StopIteration" (2.2版本不保证stick, 2.3开始才完全支持这一行为。)


# PEP 234中对iterator的讨论
Python版本：2.1
Q: StopIteration作为迭代结束方式是否太过昂贵了？
A: 对比另外的几种方式，StopIteration更高效
	- 用特殊值来标记结束。如果序列中包含这个特殊值，那么就有问题，参考C语言\n结束的字符串。
	- end()函数，意味着每次迭代都要两个函数调用，这比一个函数调用+异常检测要更昂贵。
	- 复用IndexError,这会导致真正的错误根正常的结束无法区分。

Q: 是否要在标准库中引入新的iterator类型，之后所有的iterator都应该派生自这个类型。
A: 理论上可以，但这不是python的方式。就如同字典(dictionaries)是一个映射(mapping)；但这是因为字典支持__getitem__()和其他的方便的操作，并不是因为它派生自抽象的mapping type！

# 总结
- 可迭代对象有3种方法来定义，
	- a.实现了sequence协议的 
	- b.实现了`__getitem__()`，如dict的 
	- c.实现了`__iter__()`方法来返回一个迭代器的。
- 迭代器就是实现了迭代器协议的对象，需要包含两个方法：`__iter__()`和`__next__()`。
- 迭代器协议就是指上面的两个函数。
- sequence协议是指`__getitem__(int)`和`__len__()`两个方法。

# 参考
[1]. [PEP 234 - Iterator](https://peps.python.org/pep-0234/)
[2]. [PEP 3114 – Renaming iterator.next() to iterator.__next__()](https://peps.python.org/pep-3114/)
[3]. [Python Glossary](https://docs.python.org/3/glossary.html)
[4]. [built-in iter() function](https://docs.python.org/3/library/functions.html#iter)
[5]. [Iterator wiki](https://wiki.python.org/moin/Iterator)
[6]. [⭐️ stdtypes Iterator](https://docs.python.org/3/library/stdtypes.html#typeiter)
[7]. [C Api iterator](https://docs.python.org/3/c-api/iterator.html)
[8]. [Ka-Ping Yee](http://zesty.ca/)