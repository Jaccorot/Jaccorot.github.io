---
layout: post
title:  "Android-Test-MONKEY(2)"
date:   2016-03-05 17:00:00 +0800
categories: android test
---

## AVD环境搭建
使用Android SDK开发应用程序需要进行测试，Android为开发人员提供了可以在电脑上直接测试应用程序的虚拟设备AVD（Android Virtual Device），或称作模拟器。使用AVD可以方便地对程序进行测试。

下面介绍下命令行创建AVD的步骤：

1. 安装Android SDK，并设置好环境变量。

2. 在cmd中输入`android list target`，屏幕中将显示当前可用的设备列表。

3. 开始创建`android create avd --name avdname --target 1`。
    
当有多个abi时，会遇到报错，此时你需要增加参数来制定abi
    
    报错内容：
    Valid ABIs: armeabi-v7a, x86
    Error: This platform has more than one ABI. Please specify one using --abi.

格式如下：`android create avd --name avdname --target 1 --abi armeabi-v7a`


4. 输入`android list avds`即可查看当前拥有的avd

    C:\Users\Jaccorot>android list avds
    Available Android Virtual Devices:
        Name: and60
        Path: D:\Program Files (x86)\Android\android-sdk\tools\.android\avd\and60.avd
      Target: Android 6.0 (API level 23)
     Tag/ABI: default/armeabi-v7a
        Skin: WVGA800

5. 之后可以通过`emulator –avd avdname`来启动AVD。

启动之后输入`adb devices`查看即可看到新增一个emulator

    List of devices attached
    emulator-5554   device

## 一、Monkey测试简介
Monkey测试是Android平台自动化测试的一种手段，通过Monkey程序模拟用户触摸屏幕、滑动Trackball、按键等操作来对设备上的程序进行压力测试，检测程序多久的时间会发生异常。

## 二、Monkey程序介绍
1) Monkey程序由Android系统自带，使用Java语言写成，在Android文件系统中的存放路径是：/system/framework/monkey.jar；

2) Monkeyjar程序是由一个名为“monkey”的Shell脚本来启动执行，shell脚本在Android文件系统中的存放路径是：/system/bin/monkey；
 
这样就可以通过在CMD窗口中执行: adb shell monkey ｛+命令参数｝来进行Monkey测试了。 

##  三、Monkey的基本用法

`$ adb shell monkey [options]`

如果不指定options，Monkey将以无反馈模式启动，并把事件任意发送到安装在目标环境中的全部包。下面是一个更为典型的命令行示例，它启动指定的应用程序，并向其发送1000个伪随机事件：

常用方法：

`$ adb shell monkey -p your.package.name -v number`

$ monkey -p（package的意思）  指定文件名 -v（测试的次数和频率） number（次数）
其中 package.name 可以通过查看/data/data/下的文件来确定

## 四、Monkey测试的停止条件

（1）如果先顶了Monkey运行在一个或几个特定的包上，那么它会检测试图转到它包的操作，并对其进行阻止；

（2）如果应用程序崩溃或接收到任何失控异常，Monkey将停止并报错；

（3）如果应用程序产生了应用程序不响应（application not responding）的错误，Monkey将会停止并报错。

通过多次并且不同设定下的Monkey测试才算它是一个稳定性和健壮性足够的程序。

## 五、Monkey命令参数详细说明

    1) 参数：  -p
    参数-p用于约束限制，用此参数指定一个或多个包（Package，即App）。指定
    包之后，Monkey将只允许系统启动指定的APP。如果不指定包，Monkey将允许系统启动设备中的所有APP。
    * 指定一个包： adb shell monkey -p com.htc.Weather 100
    说明：com.htc.Weather为包名，100是事件计数（即让Monkey程序模拟100次随机用户事件）。
    * 指定多个包：adb shell monkey -p com.htc.Weather –p com.htc.pdfreader  -p com.htc.photo.widgets 100
    * 不指定包：adb shell monkey 100
    　说明：Monkey随机启动APP并发送100个随机事件。
    * 要查看设备中所有的包，在CMD窗口中执行以下命令：
     >adb shell
      #cddata/data
      #ls
      
    2) 参数:  -v
    用于指定反馈信息级别（信息级别就是日志的详细程度），总共分3个级别，分别对应的参数如下表所示：
    日志级别 Level0 
    示例 adb shellmonkey -p com.htc.Weather –v 100
    说明 缺省值，仅提供启动提示、测试完成和最终结果等少量信息
     
    日志级别 Level 1
    示例 adb shellmonkey -p com.htc.Weather –v -v 100
    说明  提供较为详细的日志，包括每个发送到Activity的事件信息
     
    日志级别 Level 2
    示例 adb shellmonkey -p com.htc.Weather –v -v –v 100
    说明  最详细的日志，包括了测试中选中/未选中的Activity信息
     
    3)参数：  -s
    用于指定伪随机数生成器的seed值，如果seed相同，则两次Monkey测试所产生的事件序列也相同的。
    * 示例：
    　Monkey测试1：adb shell monkey -p com.htc.Weather –s 10 100
      Monkey 测试2：adb shell monkey -p com.htc.Weather–s 10 100
       两次测试的效果是相同的，因为模拟的用户操作序列（每次操作按照一定的先后顺序所组成的一系列操作，即一个序列）是一样的。操作序
     
    列虽   然是随机生成的，但是只要我们指定了相同的Seed值，就可以保证两次测试产生的随机操作序列是完全相同的，所以这个操作序列伪随
     
    机的；
     
    4) 参数：  --throttle <毫秒>
    用于指定用户操作（即事件）间的时延，单位是毫秒；
    * 示例：adb shell monkey -p com.htc.Weather –throttle 3000 100
      
    5) 参数：  --ignore-crashes
    用于指定当应用程序崩溃时（Force& Close错误），Monkey是否停止运行。如果使用此参数，即使应用程序崩溃，Monkey依然会发送事件，直
     
    到事件计数完成。
    * 示例1：adb shellmonkey -p com.htc.Weather --ignore-crashes 1000
      测试过程中即使Weather程序崩溃，Monkey依然会继续发送事件直到事件数目达到1000为止；
    * 示例2：adb shellmonkey -p com.htc.Weather 1000
      测试过程中，如果Weather程序崩溃，Monkey将会停止运行。
     
     
    6) 参数：  --ignore-timeouts
    用于指定当应用程序发生ANR（Application No Responding）错误时，Monkey是否停止运行。如果使用此参数，即使应用程序发生ANR错误，
     
    Monkey依然会发送事件，直到事件计数完成。
     
    7) 参数：  --ignore-security-exceptions
    用于指定当应用程序发生许可错误时（如证书许可，网络许可等），Monkey是否停止运行。如果使用此参数，即使应用程序发生许可错误，
     
    Monkey依然会发送事件，直到事件计数完成。
     
    8) 参数：  --kill-process-after-error
    用于指定当应用程序发生错误时，是否停止其运行。如果指定此参数，当应用程序发生错误时，应用程序停止运行并保持在当前状态（注意：
     
    应用程序仅是静止在发生错误时的状态，系统并不会结束该应用程序的进程）。
     
    9) 参数：  --monitor-native-crashes
    用于指定是否监视并报告应用程序发生崩溃的本地代码。
     
    10) 参数：  --pct-｛+事件类别｝｛+事件类别百分比｝
    用于指定每种类别事件的数目百分比（在Monkey事件序列中，该类事件数目占总事件数目的百分比）
     
    参数:
    使用说明:
    示例:
     
    --pct-touch ｛+百分比｝
    调整触摸事件的百分比(触摸事件是一个down-up事件，它发生在屏幕上的某单一位置)
    adb shell monkey -p com.htc.Weather--pct-touch 10 1000
     
    --pct-motion ｛+百分比｝
    调整动作事件的百分比(动作事件由屏幕上某处的一个down事件、一系列的伪随机事件和一个up事件组成)adb shell monkey -p
     
    com.htc.Weather --pct-motion 20 1000
     
    --pct-trackball ｛+百分比｝
    调整轨迹事件的百分比(轨迹事件由一个或几个随机的移动组成，有时还伴随有点击)
    adb shell monkey -p com.htc.Weather--pct-trackball 30 1000
    --pct-nav ｛+百分比｝
     
    调整“基本”导航事件的百分比(导航事件由来自方向输入设备的up/down/left/right组成)
    adb shell monkey -p com.htc.Weather--pct-nav 40 1000
     
    --pct-majornav ｛+百分比｝
    调整“主要”导航事件的百分比(这些导航事件通常引发图形界面中的动作，如：5-way键盘的中间按键、回退按键、菜单按键)
    adb shell monkey -p com.htc.Weather--pct-majornav 50 1000
     
    --pct-syskeys ｛+百分比｝
    调整“系统”按键事件的百分比(这些按键通常被保留，由系统使用，如Home、Back、Start Call、End Call及音量控制键)
    adb shell monkey -p com.htc.Weather--pct-syskeys 60 1000
     
    --pct-appswitch ｛+百分比｝
    调整启动Activity的百分比。在随机间隔里，Monkey将执行一个startActivity()调用，作为最大程度覆盖包中全部Activity的一种方法
    adb shell monkey -p com.htc.Weather--pct-appswitch 70 1000
     
    --pct-anyevent ｛+百分比｝
    调整其它类型事件的百分比。它包罗了所有其它类型的事件，如：按键、其它不常用的设备按钮、等等
    adb shell monkey -p com.htc.Weather
     
    --pct -anyevent 100 1000* 指定多个类型事件的百分比：
    adb shell monkey -p com.htc.Weather--pct-anyevent 50 --pct-appswitch 50 1000
    注意：各事件类型的百分比总数不能超过100%；
