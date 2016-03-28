---
layout: post
title:  "Fiddler中怎样修改请求和响应数据？"
date:   2016-03-26 17:00:00 +0800
categories: fiddler test
---

## 缘起

说起截获HTTP协议中的请求和响应数据并进行修改，WooYun中用的比较多的是Burp Suite， 其实，Fiddler也可以实现修改请求和响应数据的功能，而且更加好用，还支持中文URL。

当然，Burp Suite是专注于Web Security的，它在Web Penetration上的其他巨强功能也是Fiddler所不能比的，Fiddler更加倾向于是一个Web Debugger。

## 使用步骤

* 启动Fiddler Web Debugger

* 设置既能修改请求也能修改响应的自动断点

        Rules - Automatic Breakpoint - Before Requests
        快捷键：F11

    完成设置后，若在浏览器中发出请求，会被Fiddler首先截获，
    此时，在Inspectors中这个请求已经被中断，可以进行下面4个操作：

1. Breakpoint hit, tamper, then:
 这个就是目前的已被断点中断的状态，在这个状态下可以直接修改请求数据

2. Break on Response
 点击这个按钮可以中断到响应状态，在这个状态下可以直接修改响应数据

3. Run to Completion
 点击这个按钮可以完成全部的请求响应过程，将响应数据全部返回给浏览器

4. Choose Response...
 点击这个按钮并选取已经预先配置好的某个响应，
 然后再点击“Run to Completion”，即可以直接返回某个已经配置好的响应

* 设置只修改响应的自动断点

        Rules - Automatic Breakpoint - After Responses
        快捷键：Alt + F11

    完成设置后，若在浏览器中发出请求，会被Fiddler首先截获至响应状态，
    此时，在Inspectors中这个请求已经被中断，可以进行下面3个操作：

1. Break on Response
 这个按钮已经被灰掉了，因为目前已经被中断到了响应状态，
 在这个状态下可以直接修改响应数据

2. Run to Completion
 点击这个按钮可以完成全部的请求响应过程，将响应数据全部返回给浏览器

3. Choose Response...
 点击这个按钮并选取已经预先配置好的某个响应，
 然后再点击“Run to Completion”，即可以直接返回某个已经配置好的响应

* 取消自动断点

        Rules - Automatic Breakpoint - Disabled
        快捷键：Shift + F11

## 响应调试

AutoResponder


原文地址：[http://v2in.com/tamper-request-response-in-fiddler.html](http://v2in.com/tamper-request-response-in-fiddler.html)

