---
layout: post
title:  "Github Markdown基础"
date:   2016-01-11 20:00:00 +0800
categories: github, markdown
---
  Markdown可以有效的将你的易读易写的纯文本格式，转化为方便在Github上阅览HTML格式。

简单记录下基本用法如下：

1. 段落

    Markdown中的段落就是在文本之后添加一个或多个空行，连续的行认为是同一段落。

        CODE:

        今天天气不错哦。

        明天天气还可以哦。
        是吗？

    SAMPLE:

    今天天气不错哦。

    明天天气还可以哦。
    是吗？

2. 标题

    在文本前面加入“#”标签或者在文本下方输入\=或\-，即可变为转化为\<H\>标签。加N个“#”则代表\<HN\>。

    块引用用\>显示


        CODE:
        # h1 tag
        ## h2 tag
        ###### h6 tag

        h1 tag
        ======

        h2 tag
        ------

        >blockquote

        >second line

    SAMPLE:

    # h1 tag
    ## h2 tag
    ###### h6 tag

    h1 tag
    ======
    h2 tag
    ------

    >blockquote

    >second line


3. 文本格式

    前后用\*包围表示斜体，前后用\_包围表示下划线；前后用两个\*或\_包围表示加粗。

        CODE:
        今天是个*大 大*的晴天。

        今天是个_大 大_的晴天。

        今天是个**大 大**的晴天。

        今天是个__大 大__的晴天。

    SAMPLE:

    今天是个*大 大*的晴天。

    今天是个_大 大_的晴天。

    今天是个**大 大**的晴天。

    今天是个__大 大__的晴天。

4. 列表

    用\*,\+,\-和一个空格来表示无序列表；用数字+\.+空格来标示有序列表。
    在列表中采用换行+4空格(或tab)，可以作为项的同级元素。

        CODE:
        * 雅典娜
        * 紫龙

            春丽
        + 纱织
        - 沙加

        1. 星矢
        2. 冰河

    SAMPLE:
        
        * 雅典娜
        * 紫龙

            春丽
        + 纱织
        - 沙加

        1. 星矢
        2. 冰河


5. 链接

    采用\[xxx\]\(xxx\)形式。

    或\[AAA\]\[BBB\]+任意地方的\[BBB\]+:+空格+链接的形式来表示

        CODE:

        通往[百度](http://www.baidu.com)和[必应](http://cn.bing.com)的链接。

        通往[百度][1]和[必应][2]的链接。
        [1]: http://www.baidu.com "baidu"
        [2]: http://cn.bing.com "bing"

        通往[百度][baidu]的链接。
        [baidu]: http://www.baidu.com

    SAMPLE:

    通往[百度](http://www.baidu.com)和[必应](http://cn.bing.com)的链接。

    通往[百度][1]和[必应][2]的链接。
    [1]: http://www.baidu.com "baidu"
    [2]: http://cn.bing.com "bing"

    通往[百度][baidu]的链接。
    [baidu]: http://www.baidu.com


6. 图片

    格式如下：

        CODE:

        !\[alt text\]\(/images/logo-wx.png "Title"\)

        !\[alt text\]\[id\]
        \[id\]: /images/logo-wx.png "Title"

    SAMPLE:

    ![alt text](/images/logo-wx.png "Title")

    ![alt text][id]

    [id]: /images/logo-wx.png "Title"

7. 代码

    用`来前后包围显示。

        CODE:

        code标签就是`<code>`，其他的像`&mdash;`，`&#8212;`也都能正常显示。

    SAMPLE:

    code标签就是`<code>`，其他的像`&mdash;`，`&#8212;`也都能正常显示。

8. 标记语言

        CODE:
        如果想完整显示代码段而不进行转移，只需要在代码前面换行后，加四个空格或一个tab即可。

        <blockquote>
            <p>For example.</p>
        </blockquote>

    SAMPLE：

    如果需要显示代码段，只需要在代码前面加四个空格或一个tab即可

        <blockquote>
            <p>For example.</p>
        </blockquote>