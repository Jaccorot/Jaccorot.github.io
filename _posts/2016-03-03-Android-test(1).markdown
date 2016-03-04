---
layout: post
title:  "Android test(1)"
date:   2016-03-03 20:51:00 +0800
categories: android appium
---
  鉴于Android机卡顿慢，测试起来及其容易出问题，计划近期做一些关于Android的自动化测试相关学习。
  计划了解的内容有：
  1. ADB
  2. monkey
  3. monkeyrunner
  4. uiautomator
  5. robotium
  6. appium


# 1.ADB
在SDK的platform-tools文件夹下包含着Android模拟器操作的重要命令ADB，ADB的全称为Android Debug Bridge，就是调试桥的作用，借助这个工具，我们可以管理设备或手机模拟器的状态,还可以进行以下的操作：

（1）快速更新设备或手机模拟器中的代码，如应用或Android系统升级；
（2）在设备上运行Shell命令；
（3）管理设备或手机模拟器上的预定端口；
（4）在设备或手机模拟器上复制或粘贴文件。

1.1 adb启动

adb默认端口为5037,通常将adb置入环境变量的path中，即可通过cmd启动。

`adb devices` 可以查看当前连接的手机列表，如果adb未启动会自动启动

    C:\Users\Jaccorot>adb devices
    List of devices attached
    * daemon not running. starting it now on port 5037 *
    * daemon started successfully *

如果已运行，结果如下

    C:\Users\Jaccorot>adb devices
    List of devices attached
    ce97caa2        device

有时会遇到端口冲突，解决方法如下：

    1.杀进程
    C:\Users\Jaccorot>netstat -nao|findstr 5037
    TCP    127.0.0.1:5037         0.0.0.0:0              LISTENING       3676

    通过PID查看所有进程
    C:\Users\Jaccorot>tasklist /fi "PID eq 3676"

    映像名称                       PID 会话名              会话#       内存使用
    ========================= ======== ================ =========== ============
    svchost.exe                   3676 Services                   0     13,800 K

    杀死占用端口的进程
    C:\Users\Jaccorot>taskkill /pid 3676 /f
    成功: 已终止 PID 为 3676 的进程。


    2.改端口

    只要在系统环境变量中定义 ANDROID_ADB_SERVER_PORT 的值即可。

    最好选择一个5位数的端口号（10000 ~ 65535），不易重复。

    win下只要在环境变量中增加一个ANDROID_ADB_SERVER_PORT ，值填你自己定义的端口。

    linux下只要 export $ANDROID_ADB_SERVER_PORT = 自定义端口，即可。


1.2 版本信息

`adb version`

    C:\Users\Jaccorot>adb version

    Android Debug Bridge version 1.0.32

    Revision 09a0d98bebce-android

1.3 安装应用到模拟器 

`adb install [-l] [-r] <file>`

其中file是需要安装的apk文件的绝对路径。

 
1.4卸载已经安装的应用

（1）方法1：

`adb uninstall [-k] <package>`

其中package表示需要卸载的应用的包的名字，k表示是否保留应用的配置信息和cache数据。

（2）手动删除

    adb shell
    cd /data/app
    rm app.apk

1.5 进入设备或模拟器的Shell

`adb shell` 通过该命令，就可以进入设备或模拟器的Shell环境中，在这个Linux Shell中，可以执行各种Linux 的命令，另外如果只想执行一条Shell命令，可以采用以下的方式：
`adb shell [command]`
如：

    C:\Users\Jaccorot>adb shell df

    Filesystem               Size     Used     Free   Blksize
    /dev                   930.4M    76.0K   930.4M   4096
    /sys/fs/cgroup         930.4M    12.0K   930.4M   4096
    /mnt/asec              930.4M     0.0K   930.4M   4096
    /mnt/obb               930.4M     0.0K   930.4M   4096
    /system                  2.9G     1.8G     1.1G   4096
    /oem                    59.0M    44.0K    58.9M   4096
    /data                   10.5G     2.3G     8.3G   4096
    /cache                 290.6M   608.0K   290.0M   4096
    /persist                27.5M   156.0K    27.3M   4096
    /firmware               64.0M    51.3M    12.6M   16384
    /mnt/shell/emulated     10.5G     2.8G     7.7G   4096

1.6 复制文件

可以使用`adb pull` ,`adb push` 命令将文件复制到一个模拟器/设备实例的数据文件或是从数据文件中复制。install 命令只将一个apk文件复制到一个特定的位置，与其不同的是，pull 和 push 命令可让用户复制任意的目录和文件到一个模拟器/设备实例的任何位置。

从模拟器或者设备中复制文件或目录，使用如下命:

`adb pull <remote> <local>`

将文件或目录复制到模拟器或者设备，使用如下命令：

`adb push <local> <remote>`

在这些命令中， <local> 和<remote> 分别指通向自己的发展机（本地）和模拟器/设备实例（远程）上的目标文件/目录的路径。

下面是一个例子：:

`adb push foo.txt /sdcard/foo.txt`