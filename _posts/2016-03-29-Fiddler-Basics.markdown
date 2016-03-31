---
layout: post
title:  "Fiddler基础介绍"
date:   2016-03-29 17:00:00 +0800
categories: fiddler test
---

## 工作原理
  Fiddler 是以代理web服务器的形式工作的，它使用代理地址:127.0.0.1，端口:8888。当Fiddler退出的时候它会自动注销，这样就不会影响别的程序。


## 手机抓包

* Fiddler设置

1. 打开Fiddler,     Tools-> Fiddler Options 。  （配置完后记得要重启Fiddler）.

2. 选中"Decrpt HTTPS traffic",    Fiddler就可以截获HTTPS请求
![HTTPS](../static/images/20160329/20160329_fiddler_setting_https.jpg)
3. 选中"Allow remote computers to connect".  是允许别的机器把HTTP/HTTPS请求发送到Fiddler上来.
![HTTPS](../static/images/20160329/20160329_fiddler_setting_connections.jpg)

* PC设置

1. 设置无线WiFi，最好借助360WiFi做出专有WiFi，避免抓包时数据混杂，不易分辨。

2. 在命令行中用ipconfig查看所设置WiFi所对应的IP
![PC](../static/images/20160329/20160329_fiddler_wifi.jpg)

* 移动端（iOS or Android）

1. 链接PC端所分享的WiFi，并进入详细设置页面
![PHONE](../static/images/20160329/20160329_fiddler_phone_setting.jpg)

2.将代理设置为`手动`，并将上述操作中的IP填入，端口号默认为`8888`
![PHONE](../static/images/20160329/20160329_fiddler_phone_agent.jpg)

这样，就实现了网络抓包。当移动端进行网络访问时，即可查看其所交互的内容。

## Filter

切换到fiddler右侧窗口的Filters选项卡，勾选顶部的“Use Filters”，找到Hosts区域，设置以下三个选项：
![Filter](../static/images/20160329/20160329_fiddler_filter.png)

1. 第一项有三个选项，不做更改：

    “No zone filter”
  
    “Show Only Intranet Hosts”
  
    “Show Only Internet Hosts”

2. 第二个选项是只监控以下网址，文本框内输入需要过滤的域名，多个域名使用”;“分号分割。fiddler默认会检查http头中设置的host，强制显示http地址中的域名。

    如只监控易企秀相关内容，在下面的输入框里填上测试服、预发布服和文件服务器等相关域名。

    “No Host Filter”不设置hosts过滤
  
    “Hide The Following Hosts”隐藏过滤到的域名
  
    “Show Only The Following Hosts”只显示过滤到的域名
  
    “Flag The Following Hosts”标记过滤到的域名

3. 在Actions中点击`Run Filterset now`，执行筛选条件。