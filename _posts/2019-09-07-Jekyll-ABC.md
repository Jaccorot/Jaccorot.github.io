---
layout: post
title:  "通过Github Pages + Jekyll部署博客"
date:   2019-09-07 14:30:00 +0800
categories: 杂谈
tags: jekyll ,github pages
excerpt: 使用github提供的免费使用github pages，通过Jekyll模板，完成博客的搭建
---
* content
{:toc}

# 概述
>  GitHub Pages ，提供的免费的静态站点，支持Jekyll[官网点这里](https://pages.github.com/)

> Jekyll是个简单免费的blog生成工具，可以将纯文本（支持markdown
> ）转化为静态博客网站。[Jekyll中文网站在这里](http://jekyllcn.com/)，大概阅读10分钟就能搭建一个雏形，不过这个网站已经三年没更新了，补充知识还可以去[官网](https://jekyllrb.com/)了解最新动态。



# 简要步骤
## 1、Github Pages

* ① 得有全球最大交友网站github账号，再本地装好git客户端

* ② 建立一个以github账号名为名的代码仓库，\{\{username\}\}.github.io(such as jaccorot.github.io)，同时在底部Add .gitigore选择Jekyll模板，这样Jekyll产生的临时文件。

* ③ 在本地建一个文件夹，将上述clone到本地

      git clone https://github.com/username/username.github.io

* ④ 本地创建个index.html文件，并稍作编辑

      cd username.github.io
      echo "Hello World" > index.html

* ⑤ 将本地文件全部都提交至github

      git add --all
      git commit -m "Initial commit"
      git push -u origin master

* ⑥ 通过浏览器访问网址： https://\{\{username\}\}.github.io， (username替换为登录名)万事大吉!

## 2、Jekyll
* ① 安装完整的ruby环境，[具体看这里](https://jekyllrb.com/docs/installation/)
* ② 安装jekyll

      gem install jekyll
* ③ 初始化一个项目,完成后就可以将内容传到上查看效果了

      jekyll new myblog

* ④ 本地启动预览效果，执行命令，并访问  localhost:4000
     
      jekyll serve 

## 3、后续
通过jekyll本地server预览博客效果，调试完成后通过步骤1的命令上传至github

# 踩坑记录
1. gem总是超时
RubyGems在墙外面，国内很难访问到，需要换gem源，具体参照[ruby-china](https://gems.ruby-china.com/)
    
        $ gem update --system # 这里请翻墙一下
        $ gem -v
        2.6.3

        $ gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
        $ gem sources -l https://gems.ruby-china.com
        # 确保只有 gems.ruby-china.com

2. 安装最新版jekyll时，报错提示ruby版本太低   
>  Ruby version >= 2.4.0. The current ruby version is 2.3.0

安装RVM ruby版本管理器

        $ curl -L get.rvm.io | bash -s stable
        $source ~/.bashrc
        $source ~/.bash_profile 
        $rvm -v   # 查看安装是否正确
        rvm 1.27.0 (latest) by Wayne E. Seguin, Michal Papis[https://rvm.io/]
        $ruby -v # 查看Ruby当前版本
        $rvm list known # 查看ruby所有版本
        $rvm install 2.4.2  # 将ruby升级到2.4.2


# [快去看看最终效果吧](http://jaccorot.github.io)