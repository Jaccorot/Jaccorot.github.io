---
layout: post
title:  "Android-Test-Appium(4)"
date:   2016-03-11 20:00:00 +0800
categories: android test
---

## appium测试范围：

1. appium是开源的移动端自动化测试框架；
2. appium可以测试原生的、混合的、以及移动端的web项目；
3. appium可以测试ios，android应用（当然了，还有firefox os）；
4. appium是跨平台的，可以用在osx，windows以及linux桌面系统上；

## appium的技术架构

* OS: Apple’s UIAutomation
* Android 4.2+: Google’s UiAutomator
* Android 2.3+: Google’s Instrumentation. (Instrumentation support is provided by bundling a separate project, Selendroid)

## Client/Server Architecture

appium的核心其实是一个暴露了一系列REST API的server。

这个server的功能其实很简单：监听一个端口，然后接收由client发送来的command。翻译这些command，把这些command转成移动设备可以理解的形式发送给移动设备，然后移动设备执行完这些command后把执行结果返回给appium server，appium server再把执行结果返回给client。

在这里client其实就是发起command的设备，一般来说就是我们代码执行的机器，执行appium测试代码的机器。狭义点理解，可以把client理解成是代码，这些代码可以是java/ruby/python/js的，只要它实现了webdriver标准协议就可以。

这样的设计思想带来了一些好处：

1. 可以带来多语言的支持；
2. 可以把server放在任意机器上，哪怕是云服务器都可以；（是的，appium和webdriver天生适合云测试）

## appium安装

server:

  去[appium官网](http://appium.io/)即可下载对应版本的软件

client:

  推荐使用pip安装`pip install Appium-Python-Client`

## 常用方法

参照[appium-python-client](https://github.com/appium/python-client)

## 设置

* automationName：使用哪种自动化引擎。appium（默认）还是Selendroid？
* platformName：使用哪种移动平台。iOS, Android, orFirefoxOS？
* deviceName：启动哪种设备，是真机还是模拟器？iPhone Simulator, iPad Simulator, iPhone,Retina 4-inch, Android Emulator, Galaxy S4, etc...
* app：应用的绝对路径，注意一定是绝对路径。如果指定了appPackage和appActivity的话，这个属性是可以不设置的。另外这个属性和browserName属性是冲突的。
* browserName：移动浏览器的名称。比如Safari' for iOS and 'Chrome', 'Chromium', or 'Browser' for Android；与app属性互斥。
* udid：物理机的id。比如1ae203187fc012g。


下面这些属性是android平台特定的：

* appActivity：待测试的app的Activity名字。比如MainActivity, .Settings。注意，原生app的话要在activity前加个"."。
* appPackage：待测试的app的java package。比如com.example.android.myApp, com.android.settings。

参照如下：

    from appium import webdriver
    desired_caps = {}
    desired_caps['platformName'] = 'Android'
    desired_caps['platformVersion'] = '4.2'
    desired_caps['deviceName'] = 'Android Emulator'
    desired_caps['appPackage'] = 'com.android.calculator2'
    desired_caps['appActivity'] = '.Calculator'

    driver = webdriver.Remote('http://localhost:4723/wd/hub', desired_caps)

## 元素定位

可以通过andriod_sdk_path\tools下面的UI Automator Viewer进行元素解析。

然后再通过下述方法来定位：
* find_element_by_accessibility_id
* find_elements_by_accessibility_id
* find_element_by_android_uiautomator
* find_element_by_android_uiautomator

具体如下:
    el = self.driver.find_element_by_android_uiautomator('new UiSelector().description("Animation")')
    self.assertIsNotNone(el)
    els = self.driver.find_elements_by_android_uiautomator('new UiSelector().clickable(true)')
    self.assertIsInstance(els, list)

    el = self.driver.find_element_by_accessibility_id('Animation')
    self.assertIsNotNone(el)
    els = self.driver.find_elements_by_accessibility_id('Animation')
    self.assertIsInstance(els, list)