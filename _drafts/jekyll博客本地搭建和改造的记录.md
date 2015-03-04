* 首先要能在win7上搭建一个可以调试的环境
然后开始对不满意的地方开始更改 ，从中了解这个框架的机制和github上可以用的插件

##jekyll 环境的搭建

[官网安装教程](http://jekyllrb.com/docs/installation/)

由于windows不是官方支持(例如编码的坑)，所以有个单门的[安装说明](http://jekyllrb.com/docs/windows/#installation)主要就是安装ruby，jekyll还有必须的插件这些，这些[文档](http://jekyll-windows.juthilo.com/)上很详细,每一步都有图文解说。

简单的步骤描述：

* Ruby windows版本的[下载页](http://rubyinstaller.org/downloads/),  下载(这里是2.0 版本)之后安装，并添加到path路径中

* [gem下载地址](https://rubygems.org/pages/download), 下载之后安装 ruby setup.rb , gem类似python的pip 用来安装第三方包

* 安装jekyll
    - **gem install jekyll --pre**
    - **gem install jekyll -v '2.0.0.alpha.1'**




