---
layout: post
date: 2014-10-07 17:58:48 +0800
title:  MongoDB 纪要
tags: mongo
---


##体系结构
一般情况下一个实例多个数据库，也可以多个实例多个数据库

###逻辑结构(从小到大，逻辑结构主要面向开发)
* document  (相当于mysql  record)
* collection   (相当于mysql  table)
* database   (相当于mysql  database)

###数据结构
对于一个数据库，存储为一个.ns文件和多个.0,.1...类似文件。

例如一个test数据 有test.ns , test.0, test.1, test.2....文件组成。

    2014/09/22 周一  14:34        16,777,216 test.0
    2014/09/22 周一  14:34        33,554,432 test.1
    2014/09/22 周一  14:34        16,777,216 test.ns

##启动方式
* 带参数启动  /Apps/mongo/bin/mongod --dbpath=/data/db
* 守护启动 加 --fork参数 /Apps/mongo/bin/mongod --dbpath=/data/db --fork
* 指定配置文件启动方式  ./mongod -f /etc/mongodb.cnf


##数据操作
###插入
* 单个插入
* 批量插入

tips：插入的时候会生成 '_id',适合分布式存储

###查询
* 单个查询 findOne()
* 多个记录查询
* 使用游标
* 条件查询
    * 根据某些字段的值 类似 where name="lzz"
    * 返回某些列(指定的列)，或者屏蔽某些列
    * $gt, $lt, $gte, $lte
    * $all 集合比较（数组）
    * $exists 某个字段是否存在
    * null 值处理
    * $mod 根据运算结果作为查询条件
    * $ne 不等于
    * $in 注意和$all的区别
    * $nin 不包含
    * $size 元素长度匹配（数组）
    * $not 取反 经常和正则搭配使用
    * 使用正则匹配字段
    * 查询条件是个函数或者是 $where
    * $slice 数组切片
    * 查询子文档
* 返回值
    * count 返回记录数
    * 返回指定数量的记录 limit(n)
    * skip 返回记录的起点
    * 根据字段排序 升序倒序
    * 游标
    * 存储过程
	
<img src="/images/mongodb_query.png" >

###修改
* 修改某个字段
* 插入修改，没有这个记录自动插入一个
* 修改函数，常用的修改操作，递增，插入list等

pymongo 默认修改的是一个记录，如果要修改多条记录必须要明确给出参数。

<img src="/images/mongodb_update.png" >

###删除
* 单个删除，批量删除

##其他
* 默认是不安全操作，对于一致性要求高的操作需要用安全操作
* 及时查询等要使用同一个连接，不在同一个连接中可能无法及时查到别的连接的插入










