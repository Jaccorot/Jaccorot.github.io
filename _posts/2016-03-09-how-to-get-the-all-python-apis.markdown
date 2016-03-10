---
layout: post
title:  "How To Get The All Python Apis"
date:   2016-03-09 17:00:00 +0800
categories: python
---

经常遇到某个python库，但是不记得其中方法的情况，此时，只需运行一行简单命令，即可查看所有的apis。


在CMD中输入`python -m pydoc -p 4567`
简单解释一下：

* python -m pydoc表示打开pydoc模块，pydoc是查看python文档的首选工具；
* -p 4567表示在4567端口上启动server;

然后在浏览器中访问http://localhost:4567/，此时应该可以看到python中所有的Modules.

比如查找selenium的方法：

	按ctrl+f，输入selenium,定位到selenium文档的链接，然后点击进入到
	http://localhost:4567/selenium.html这个页面

这就是selenium文档所在的位置了，接下来便可以根据自己的需要进行查看了。
