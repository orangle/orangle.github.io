---
layout: post
date: 2014-09-09 12:30:48 +0800
title: Python解释器的几种实现版本
tags: python
---

>我们都知道python的解释器有很多种实现方式，有C的，java的，还有python的等等，对应的也就是Cpython，Jython，一直比较火的PyPy ，今天就来盘点下这些版本（不一定非常全）

###CPython
**CPython** 是默认的python实现，环境或者是解释器(你喜欢哪个就那么叫)。脚本大多数情况下都运行在这个解释器中。
CPython是官方的python解释器，完全按照python的规格和语言定义来实现，所以被当作其他版本实现的参考版本。CPython是用C语言写的，当执行代码的时候Pythond代码会被转化成字节码（bytecode）。所以CPython是个字节码解释器。当我们从Python官网下载安装包安装，或者是通过类似 "apt-get" 或者 "yum"工具安装的时候，安装的都是CPython版本。

###[PyPy](http://pypy.org/)
**PyPy ** 是一个很多地方都和CPython很像的实现，但是这个解释器本身就是由Python写成。也就是说开发者们用Python写了一个Python解释器。然而这个解释器的代码先转化成C，然后在编译。PyPy被认为要比CPython性能更好。因为CPython会把代码转化成字节码，PyPy会把代码转化成机器码。

###Psyco
**Psyco** 是一个类似PyPy，但是很好的解释器。现在已经被PyPy取代了，有可能的话，使用PyPy来代替Psyco。

###[Jython](http://www.jython.org/)
**Jython**是用java实现的一个解释器。Jython允许程序员写Python代码，还可以把java的模块加载在python的模块中使用。Jython使用了JIT技术，也就是说运行时Python代码会先转化成Java 字节码（不是java源代码），然后使用JRE执行。程序员还可以用Jython把Python代码打成jar包，这些jar和java程序打包成的jar一样可以直接使用。这样就允许Python程序员写Java程序了。但是呢，必须要知道哪些Java模块可以在Jython中使用，然后使用Python的语法就可以写程序了。Jython兼容python2，也可以使用命令行来写交互式程序。

###[IronPython](http://ironpython.net/)
**IronPython** 是使用C#语言实现，可以使用在.NET 和 Mono 平台的解释器。IronPython 是兼容 Silverlight 的，配合Gestalt 就可以直接在浏览器中执行。IronPython也是兼容Python2的。

tip: Mono 是提供.NET-compatible 工具的开源框架。

###[CLPython](http://common-lisp.net/project/clpython/)
**CLPython** 是用 Common Lisp实现的一个解释器，现在不提倡使用。它允许 Python 和 Common Lisp 的代码混合使用。 跟Python2兼容。

###PyS60
**PyS60** (Python for S60) 是诺基亚 S60 平台的一个实现版本，不赞成使用。


###[ActivePython](http://www.activestate.com/activepython)
**ActivePython** 是基于CPython然后添加一系列拓展的一个实现。是由ActiveState发布的。Python2 和 Python3 都兼容。


###[Cython](http://cython.org/)
**Cython**(不是CPython)是一个允许把Python代码转化成C/C++代码或者使用各种各样的C/C++模块/文件的实现。换句话说，Cython是C/C++ 和Python的一个桥梁。Cython也是Python的一种方言。开发者也可以使用Cython来执行Python脚本，并且执行效率比CPython更快。另外，开发者可以写一个Python脚本，使用Cython来编译成（linux上*.so 或者是Windows上的*.dll）类库，然后当作一个Python模块来使用。Cython脚本使用*.pyx作为拓展名。Cython兼容Python2和Python3。

tips: Python 模块 modules 和 类库libraries是一个东西，只是叫法不同。

###[QPython ](http://qpython.com/)
**QPython** 是CPython解释器的一个安卓接口。QPython 来自Python的安卓模块。可以在 Google Play中找到QPython。

###[Kivy](http://kivy.org)
**Kivy** 是一个开源的框架(使用Python解释器)，它可以运行在 Android, iOS, Windows, Linux, MeeGo, Android SDK, 和 OS X平台上。
支持Python3， 开发者正在开发其兼容Cython上的Python3。


###[SL4A]((https://github.com/damonkohler/sl4a))
**SL4A** (Scripting Layer for Android) 是一个允许安卓上执行各种脚本语言的兼容层。SL4A 有很多的模块，我们比较关注的是“Py4A” (Python for Android)。 Py4A 是安卓平台上的一种CPython。


###其他
还有很多其他的不同实现。例如WPython，DSPython 请参见 [Wiki](https://wiki.python.org/moin/PythonImplementations)。

orangleliu#gmail.com
REF：
[Python Tools and Software](http://www.linux.org/threads/python-tools-and-software.6372/)
[Wiki](https://wiki.python.org/moin/PythonImplementations)
