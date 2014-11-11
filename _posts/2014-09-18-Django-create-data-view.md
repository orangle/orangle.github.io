---
layout: post
date: 2014-09-18 18:30:48 +0800
title: Django中建立数据库视图
tags: django
---

>Django中没有建立视图的接口，如果要建立一个视图需要一些手动的改变。
这里使用的Django 版本>1.5， 使用的数据库为mysql

###第一步
建立视图,例如视图的名称叫做 user_info

###第二步
model中这么写：

    class MyModel(models.Model):
        ...
        class Meta:
             managed = False
             db_table = "user_info"

这样就可以把视图经过orm变成对象了。

REF:
[creating django model for existing database/sql view?](http://stackoverflow.com/questions/2245956/creating-django-model-for-existing-database-sql-view)

[Django models文档](https://docs.djangoproject.com/en/dev/ref/models/options/#django.db.models.Options.managed)

