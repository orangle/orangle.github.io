---
layout: post
date: 2015-01-11 20:17:48 +0800
title: 使用autopy 自动播放google doodle
tags: happy
---

>早前，在 Google 的首页上，谷歌为了纪念电吉他之父莱斯·保罗 96 周年诞辰，特意做了一个很有意思的Doodle，
这个 谷歌电吉他Logo可以让用户在其上面弹奏吉他。虽然是一个细小的功能，但却大受欢迎！

- [google doodle 演示地址](http://www.iplaysoft.com/google-guitar-doodle.html)

<iframe width="640" height="230" scrolling="no" style="border:3px solid #eee;display:block;margin:10px auto;" src="http://www.iplaysoft.com/plus/others/google-guitar-doodle/recordable-guitar.htm"></iframe>

- [google 的地址 可能需要翻墙](http://www.google.com/logos/2011/lespaul.html)
- [别人整理的源码地址](http://pan.baidu.com/s/1kTmSsrP)  不过在本地浏览器直接打开没有声音，在网站上没问题
- 更多的信息 请搜索 google doodle + 关键词

大家给了很多曲谱，然后我就想打开这个界面之后让他自己播放，找了下js的方案，模拟
键盘点击不是很好做，于是想着用python模拟鼠标点击浏览器之后，在自动敲击键盘就可以
自动播放doodle 音乐了。

代码如下：

{% highlight ruby linenos %}
    # -*- encoding=utf-8 -*-
    #author: orangleliu@gmai.com
    #date: 2015-01-10
    import autopy
    import time
    import sys

    '''

    模拟点击键盘  自动弹奏google doodle 曲子
    '''

    #隐形的翅膀
    yinxingdechibang = "358787 6568321 11186532122 358787 6568321 1118653211"

    #小星星
    xiaoxingxing = "1155665 4433221 5544332 5544332 1155665 4433221"

    #沧海一声笑
    canghai = "pouyt uytew wewetyuop ppouyty"

    #天空之城
    tiankong = "6787807 365685 254573 874477 6787807 365685 34878908 876756 1232352 5878007 678789855 43213 376321 2125"

    #国歌
    guoge = "qr rrrqwer r yrtyi i yyryiytt o i t y iy iytyr y qwrryyiittw t qr ry yi rtiio i yriiiy r q r yriiiy r q r q r q r r"

    #月亮之上
    yueliang = "3686 3675 53686565326553 3686 89865 36865326656"

    def clickbower(mkey):
        time.sleep(0.5)
        autopy.key.tap(mkey)

    if __name__ == "__main__":
        #输入曲子名称 默认是天空之城
        try:
            music = sys.argv[1]
            musicq = eval(music)
        except Exception as e:
            musicq = tiankong

        print "now play music ..... %s"%musicq
        autopy.mouse.click()
        keys = list(musicq)*2
        print keys
        for i in keys:
            clickbower(i)
{% endhighlight %}

使用的方法也大致截图演示了下，虽然听起来还是很僵硬，不过挺开心的，python实现一个东西真是简单啊。

![doodle](/images/google_musicpng.png "script useing")

听着程序自动弹奏的音乐感觉挺新奇，嘿嘿。
**有一点不好，鼠标要放在要事先放在doodle上（最好是doodle上，不然和当前网页的快捷键冲突了就会有惊喜啦），中间还不能移动鼠标 --！**



