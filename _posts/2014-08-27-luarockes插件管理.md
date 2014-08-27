---
layout: post
date: 2014-08-27 20:17:48 +0800
title: lua插件管理工具LuaRocks
tags: lua
---

[一个关于LuaRocks的ppt](http://www.lua.org/wshop13/Muhammad.pdf)
[参考](http://www.linuxidc.com/Linux/2014-01/95501.htm)
这里的环境是ubuntu， 只是安装了lua5.2 ，还没有安装其他包。

###资源
* 官网：http://luarocks.org/
* 扩展列表：http://luarocks.org/repositories/rocks/
* 安装说明：http://luarocks.org/en/Installation_instructions_for_Unix(有很多的配置选项)

###install
总是提示--with-lua
其实是需要找到lua.h 文件，可以通过find命令找到，然后在configure中配置，例如我这里是/usr/local/lua/include/lua.h

####原始的安装编译流程

    git clone git://github.com/keplerproject/luarocks.git
    cd luarocks/
    ./configure --with-lua=/usr/local/lua  #注意结尾不要用/
    make build
    make build
    luarocks --help  #就能看到帮助和版本信息了

####安装一个库测试

luarocks install luasocket
...
luasocket 3.0rc1-1 is now built and installed in /usr/local (license: MIT)

安装完成。



