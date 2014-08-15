---
layout: post
date: 2014-08-15 23:59:48 +0800
title:  Django的orm中get和filter的不同
tags: django
---

>Django的orm框架对于业务复杂度不是很高的应用来说还是不错的，写起来很方面，用起来也简单。对于新手来说查询操作中最长用的两个方法get和filter有时候一不注意就会犯下一些小错误。那么今天就来小节下这两个方法使用上的不同。


我常用的是1.5版本的django，就以此为例来说说吧。

##文档
首先对比下两个函数[文档](https://docs.djangoproject.com/en/1.5/ref/models/querysets/)上的解释。

**get**
>Returns the object matching the given lookup parameters, which should be in the format described in Field lookups.

>get() raises MultipleObjectsReturned if more than one object was found. The MultipleObjectsReturned exception is an attribute of the model class.

>get() raises a DoesNotExist exception if an object wasn’t found for the given parameters. This exception is also an attribute of the model class


**filter**
>Returns a new QuerySet containing objects that do not match the given lookup parameters.

>The lookup parameters (**kwargs) should be in the format described in Field lookups below. Multiple parameters are joined via AND in the underlying SQL statement, and the whole thing is enclosed in a NOT().

*  输入参数
**get**  的参数只能是model中定义的那些字段，只支持严格匹配
**filter**  的参数可以是字段，也可以是扩展的where查询关键字，如in，like等

*  返回值
**get** 返回值是一个定义的model对象<br>
**filter** 返回值是一个新的QuerySet对象，然后可以对QuerySet在进行查询返回新的QuerySet对象，支持链式操作
        QuerySet一个集合对象，可使用迭代或者遍历，切片等，但是**不等于list类型(使用一定要注意)**

*  异常
**get**  只有一条记录返回的时候才正常,也就说明get的查询字段必须是主键或者唯一约束的字段。当返回多条记录或者是没有找到记录的时候都会抛出异常<br>
**filter** 有没有匹配的记录都可以








