---
layout: post
date: 2015-03-11 20:17:48 +0800
title:  nginx统计用户下载文件情况
tags: nginx
---

> 有一个需求是统计文件是否被用户完整下载，因为是web应用，用js没有找到实现方案，于是搜索下nginx的实现方案，把简单的探索过程记录下。

###实验一

* 最原始的思路，查看日志，下载了一个文件之后我们看日志的传输的文件大小跟文件原始的大小是否一致
* 测试要下载的文件的大小

![测试文件](/images/filez_size.png)

* 一次完整下载的log 跟一次没下载完成的log，可以通过对比传输字节的大小来判断

![测试结果](/images/nginx_log.png)

这种方式就是根据日志来做统计，每隔一段时间分析日志得到结果，有些麻烦，时效性不好。


###实验二：

找了相关的博客

*  [Counting-100-completed-downloads](https://www.linkedin.com/groups/Counting-100-completed-downloads-using-2000893.S.191692888)
使用post_action（下面使用的方式）

*  [Nginx-post-action-to-trigger-successfully-download-file](http://www.tipstuff.org/2012/08/Nginx-post-action-to-trigger-successfully-download-file.html) 遇到的问题 [nginx-post-action-proxy-pass-track-downloading-file](http://serverfault.com/questions/535429/nginx-post-action-proxy-pass-track-downloading-file)

*  使用 x-accel-redirect
    + [nginx-x-accel-redirect-php-rails](http://kovyrin.net/2006/11/01/nginx-x-accel-redirect-php-rails/) 英文
    + [利用nginx的x-accel-redirect头实现下载控制](http://bianbian.org/154/利用nginx的x-accel-redirect头实现下载控制（附带php和rails实例代码）/) 中文

大概的流程：

![结构](/images/ngix.jpg)

主要的工作就是2个
1 修改nginx的配置，把下载文件的信息转发到统计服务或者url
2 统计服务记录和判断文件下载状态

**这里的重点是使用nginx 的post_action参数， 在下载请求结束之后把下载的情况发送给另一个统计服务，由统计服务来判断文件下载的情况**



配置类似

    location / {
        limit_rate 20k;
        post_action /afterdownload;
    }
    location /afterdownload {
        proxy_pass http://127.0.0.1:8888/counting?FileName=$uri&ClientIP=$remote_addr&body_bytes_sent=$body_bytes_sent&status=$request_completion;
        internal;
    }

然后写个一个flask 来接收统计请求

    #!/usr/bin/python
    #-*- coding:utf-8 -*-
    ############################
    #File Name: counting_file.py
    #Author: orangleliu
    #Mail: orangleliu@gmail.com
    #Created Time: 2015-03-11 16:41:05
    #License: MIT
    ############################
    from flask import Flask
    app = Flask(__name__)
    @app.route("/counting")
    def counting():
        return ""
    if __name__ == "__main__":
        app.run(port=8888, debug=True)

访问的日志

    lzz@ubuntu:code$ python counting_file.py
     * Running on http://127.0.0.1:8888/
     * Restarting with reloader
    127.0.0.1 - - [11/Mar/2015 16:45:15] "GET /counting?FileName=/afterdownload&ClientIP=10.0.1.16&body_bytes_sent=0&status=OK HTTP/1.0" 200 -
    127.0.0.1 - - [11/Mar/2015 16:47:51] "GET /counting?FileName=/afterdownload&ClientIP=10.0.1.16&body_bytes_sent=245760&status= HTTP/1.0" 200 -

只要在flask中做处理就可以统计用户下载的情况了。
上面的文章也说了，当用户使用多个连接下载的时候可能就有问题了，会重复统计，结果也会不准确，所以还有很多改进空间.
