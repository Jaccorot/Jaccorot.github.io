---
layout: post
title:  "Android-Test-MonkeyRunner(3)"
date:   2016-03-08 17:00:00 +0800
categories: android test
tags: MonkeyRunner
---
* content
{:toc}

## 一、什么是monkeyrunner

monkeyrunner工具提供了一个API，使用此API写出的程序可以在Android代码之外控制Android设备和模拟器。通过monkeyrunner，您可以写出一个Python程序去安装一个Android应用程序或测试包，运行它，向它发送模拟击键，截取它的用户界面图片，并将截图存储于工作站上。monkeyrunner工具的主要设计目的是用于测试功能/框架水平上的应用程序和设备，或用于运行单元测试套件，但您当然也可以将其用于其它目的。

## 二、monkeyrunner工具同Monkey工具的差别

Monkey：

Monkey工具直接运行在设备或模拟器的adb shell中，生成用户或系统的伪随机事件流。

monkeyrunner：

monkeyrunner工具则是在工作站上通过API定义的特定命令和事件控制设备或模拟器。

## 三、monkeyrunner的测试类型

1、多设备控制：monkeyrunner API可以跨多个设备或模拟器实施测试套件。您可以在同一时间接上所有的设备或一次启动全部模拟器（或统统一起），依据程序依次连接到每一个，然后运行一个或多个测试。您也可以用程序启动一个配置好的模拟器，运行一个或多个测试，然后关闭模拟器。

2、 功能测试： monkeyrunner可以为一个应用自动贯彻一次功能测试。您提供按键或触摸事件的输入数值，然后观察输出结果的截屏。

3、 回归测试：monkeyrunner可以运行某个应用，并将其结果截屏与既定已知正确的结果截屏相比较，以此测试应用的稳定性。

4、 可扩展的自动化：由于monkeyrunner是一个API工具包，您可以基于Python模块和程序开发一整套系统，以此来控制Android设备。除了使用monkeyrunner API之外，您还可以使用标准的Python os和subprocess模块来调用Android Debug Bridge这样的Android工具。

## 四、运行monkeyrunner

您可以直接使用一个代码文件运行monkeyrunner，抑或在交互式对话中输入monkeyrunner语句。不论使用哪种方式，您都需要调用SDK目录的tools子目录下的monkeyrunner命令。如果您提供一个文件名作为运行参数，则monkeyrunner将视文件内容为Python程序，并加以运行；否则，它将提供一个交互对话环境。

monkeyrunner的命令语法为：

`monkeyrunner -plugin <plugin_jar> <program_filename> <program_options>`

## 五、实例

下面为以eqxiu.apk为例,进行了若干简单操作：

    # coding=utf-8

    from com.android.monkeyrunner import MonkeyRunner, MonkeyDevice, MonkeyImage
    import sys


    # 连接手机
    print ("Connecting device.....")
    device = MonkeyRunner.waitForConnection()
    if not device:
        print ("Please connect a device to start!")
        sys.exit()
    else:
        print ("Device Connected successfully!")


    # 安装apk
    try:
        device.installPackage('D:\desktop\Android_package\eqxiu-2.2.1.0016.apk')
        print ("Package installed successfully! ")
    except:
        print ("Package installation failed!")
        sys.exit()

    result = device.takeSnapshot()
    result.writeToFile('D:/project/monkey_runner_tests/1.png', 'png')

    package = 'cn.knet.eqxiu'
    activity = '.activity.SplashActivity'
    # device.startActivity('cn.knet.eqxiu/.activity.SplashActivity')

    for i in range(1, 5):
        # 打开已安装的应用
        print ("Open installed package for the %d time" % i)
        device.startActivity(component=package + '/' + activity)
        # 等待5秒
        print ("wait 5 sec")
        MonkeyRunner.sleep(5)
        # 关闭应用
        print("Quit the application")
        device.press("KEYCODE_BACK", device.DOWN_AND_UP)
        print ("wait 5 sec")
        MonkeyRunner.sleep(5)

    # 卸载APK
    try:
        print("Uninstall package!")
        device.removePackage("cn.knet.eqxiu")
        print("Uninstall package successfully")
    except:
        print ("Package unintall failed!")

## 六、方法详解

### 导入需要的模块
    import sys
    from com.android.monkeyrunner import MonkeyRunner as mr
    from com.android.monkeyrunner import MonkeyDevice as md
    from com.android.monkeyrunner import MonkeyImage as mi
    如果给导入的模块起了别名，就应该使用别名，而不能使用原名，否则会出现错误。
    比如连接设备或模拟器，起了以上别名后，命令应该如下：
    device=mr.waitForConnection() 

    也可以采用以下方式
    from com.android.monkeyrunner import MonkeyRunner,MonkeyDevice,MonkeyImage

    也可以采用这种方式
    import com.android.monkeyrunner
    但是在使用时，就显得特别麻烦
    device=com.android.monkeyrunner.MonkeyRunner.waitForConnection() 
    我们也可以给它一个别名
    import com.android.monkeyrunner as cam
    但是在使用时，就显得特别麻烦
    device=cam.MonkeyRunner.waitForConnection()

### 等待连接到设备
    与模拟器连接，返回monkeydevice对象,代表连接的设备。没有报错的话说明连接成功。
    参数1：超时时间，单位秒，浮点数。默认是无限期地等待。
    参数2：串deviceid，指定的设备名称。默认为当前设备（手机优先，比如手机通过USB线连接到PC、
    其次为模拟器）。
    默认连接：device=MonkeyRunner.waitForConnection()
    参数连接：device = mr.waitForConnection(1.0,'emulator-5554')

### 向设备或模拟器安装要测试的APK
    device.installPackage('myproject/bin/MyApplication.apk') #参数是相对或绝对APK路径
    路径级别用“/”，不能用“\”，比如d:\www\a.apk，而应该写成d:/www/a.apk
    安装成功返回true,此时查看模拟器我们可以在IDLE界面上看到安装的APK的图标了。


### 从设备中删除指定的软件包，包括其相关的数据和调整缓存
    device.removePackage('myproject/bin/MyApplication.apk')
    或者device.removePackage("cn.knet.eqxiu")
    删除成功返回true。


### 启动任意的Activity
    device.startActivity(component="your.www.com/your.www.com.TestActivity")
    或者
    device.startActivity(component="your.www.com/.TestActivity")

    此时可以向模拟器发送如按键、滚动、截图、存储等操作了。


    执行一个adb shell命令，并返回结果，如果有的话
    device.shell("...")

### 暂停目前正在运行的程序指定的秒数
    MonkeyRunner.sleep(秒数，浮点数)

### 获取设备的屏蔽缓冲区，产生了整个显示器的屏蔽捕获。（截图）
    result=device.takeSnapshot()
    返回一个MonkeyImage对象（点阵图包装），我们可以用以下命令将图保存到文件
    result.writeToFile('takeSnapshot\\result1.png','png')

### 写文件MonkeyImage
    MonkeyImage.writeToFile(参数1:输出文件名，也可以包括路径,参数2:目标格式)
    写成功返回true，否则返回false

### 字符串发送到键盘
    键盘上的类型指定的字符串，这相当于要求每个字符串中的字符按（键码，DOWN_AND_UP）.
    device.type('字符串')

### 唤醒设备屏幕（在设备屏幕上唤醒）
    device.wake()

### 重新引导到指定的引导程序指定的设备
    device.reboot()

### 在指定位置发送触摸事件（x,y的单位为像素）
    device.touch(x,y,TouchPressType-触摸事件类型)

### 发送到指定键的一个关键事件
    device.press(参数1:键码,参数2:触摸事件类型)
    参数1：见android.view.KeyEvent
    参数2，如有TouchPressType()返回的类型－触摸事件类型，有三种。
    1、DOWN 发送一个DOWN事件。指定DOWN事件类型发送到设备，对应的按一个键或触摸屏幕上。
    2、UP 发送一个UP事件。指定UP事件类型发送到设备，对应释放一个键或从屏幕上抬起。
    3、DOWN_AND_UP 发送一个DOWN事件，然后一个UP事件。对应于输入键或点击屏幕。
    以上三种事件做为press()或touch()参数。原英文如下：
    use this with the type argument of press() or touch() to send a down event.

    为了模拟输入键，发送DOWN_AND_UP。

    参数1的部分具体内容逻辑：
    按下HOME键 device.press('KEYCODE_HOME',MonkeyDevice.DOWN_AND_UP) 
    按下BACK键 device.press('KEYCODE_BACK',MonkeyDevice.DOWN_AND_UP) 
    按下下导航键 device.press('KEYCODE_DPAD_DOWN',MonkeyDevice.DOWN_AND_UP) 
    按下上导航键 device.press('KEYCODE_DPAD_UP',MonkeyDevice.DOWN_AND_UP) 
    按下OK键 device.press('KEYCODE_DPAD_CENTER',MonkeyDevice.DOWN_AND_UP)


    device.press('KEYCODE_ENTER',MonkeyDevice.DOWN_AND_UP)#输入回车
    device.press('KEYCODE_BACK',MonkeyDevice.DOWN_AND_UP)#点击返回

    home键 KEYCODE_HOME 
    back键 KEYCODE_BACK 
    send键 KEYCODE_CALL 
    end键 KEYCODE_ENDCALL 
    上导航键 KEYCODE_DPAD_UP 
    下导航键 KEYCODE_DPAD_DOWN 
    左导航 KEYCODE_DPAD_LEFT 
    右导航键 KEYCODE_DPAD_RIGHT  
    ok键 KEYCODE_DPAD_CENTER 
    上音量键 KEYCODE_VOLUME_UP  
    下音量键 KEYCODE_VOLUME_DOWN 
    power键 KEYCODE_POWER 
    camera键 KEYCODE_CAMERA 
    menu键 KEYCODE_MENU 


## 七、脚本录制

写脚本时不容易定位对应点的坐标和操作，可以通过monkey_recorder.py,
来通过鼠标点击模拟器来记录相关信息，同时也可通过monkey_playback.py进行事件还原。

在Android SDK的tools文件夹中，运行

`monkeyrunner path\to\monkey_recorder.py`

来进行录制，
将操作记录另存为一个文件。然后运行

`monkeyrunner path\to\monkey_playback.py path\to\record\file`

进行回放。

据传言在SDK中有这两个文件，但是本人并没有找到，在网上搜了下，脚本如下：

monkey_recorder.py 

    #!/usr/bin/env monkeyrunner
    # Copyright 2010, The Android Open Source Project
    #
    # Licensed under the Apache License, Version 2.0 (the "License");
    # you may not use this file except in compliance with the License.
    # You may obtain a copy of the License at
    #
    # http://www.apache.org/licenses/LICENSE-2.0
    #
    # Unless required by applicable law or agreed to in writing, software
    # distributed under the License is distributed on an "AS IS" BASIS,
    # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    # See the License for the specific language governing permissions and
    # limitations under the License.
    from com.android.monkeyrunner import MonkeyRunner as mr
    from com.android.monkeyrunner.recorder import MonkeyRecorder as recorder
    device = mr.waitForConnection()
    recorder.start(device)

monkey_playback.py#!/usr/bin/env monkeyrunner

    # Copyright 2010, The Android Open Source Project
    # Licensed under the Apache License, Version 2.0 (the "License");
    # you may not use this file except in compliance with the License.
    # You may obtain a copy of the License at
    #
    #     http://www.apache.org/licenses/LICENSE-2.0
    #
    # Unless required by applicable law or agreed to in writing, software
    # distributed under the License is distributed on an "AS IS" BASIS,
    # WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    # See the License for the specific language governing permissions and
    # limitations under the License.
    import sys

    from com.android.monkeyrunner import MonkeyRunner
    # The format of the file we are parsing is very carfeully constructed.
    # Each line corresponds to a single command.  The line is split into 2
    # parts with a | character.  Text to the left of the pipe denotes
    # which command to run.  The text to the right of the pipe is a python
    # dictionary (it can be evaled into existence) that specifies the
    # arguments for the command.  In most cases, this directly maps to the
    # keyword argument dictionary that could be passed to the underlying
    # command.
    # Lookup table to map command strings to functions that implement that
    # command.
    CMD_MAP = {

        'TOUCH': lambda dev, arg: dev.touch(**arg),

        'DRAG': lambda dev, arg: dev.drag(**arg),

        'PRESS': lambda dev, arg: dev.press(**arg),

        'TYPE': lambda dev, arg: dev.type(**arg),

        'WAIT': lambda dev, arg: MonkeyRunner.sleep(**arg)

        }
    # Process a single file for the specified device.


    def process_file(fp, device):

        for line in fp:
            (cmd, rest) = line.split('|')
            try:
                # Parse the pydict
                rest = eval(rest)
            except:
                print 'unable to parse options'
                continue

            if cmd not in CMD_MAP:
                print 'unknown command: ' + cmd
                continue

            CMD_MAP[cmd](device, rest)


    def main():

        file = sys.argv[1]
        fp = open(file, 'r')
        device = MonkeyRunner.waitForConnection()

        process_file(fp, device)
        fp.close()

    if __name__ == '__main__':
        main()
