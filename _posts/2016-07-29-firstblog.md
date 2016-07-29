---
layout: post
title: "开通博客的第一天"
description: 
---
今天是开通博客的第一天，以前没写过博客，也没接触过jekyll，好容易上班里挤出些时间研究了两天，才从jekyll主题集合网址：[Jekyll Themes & Templates](http://jekyllthemes.io/)扒下一个模板，仅仅修改了下_config.yml中关于个人信息的一些配置,
修改了rakefile文件让本地可以通过rake创建blog文件。想来惭愧，一些页面的整改以后慢慢再处理吧。今天简单总结下jekyll+githup建立个人站点的步骤:  


## git/github准备
* 注册并登陆github账户[*github*](https://github.com/)
* 创建github repository [*官方文档*](https://guides.github.com/activities/hello-world/)
    + repository名称应为 USERNMAE.github.com .(**USERNAME:同于注册的github账户名称**)
* 安装[*Git*](https://git-scm.com/)/[*Github Desktop*](https://desktop.github.com/)--我使用git


## [jekyll安装](http://jekyllcn.com/)
* 安装ruby(我用的windows安装[ruby&devkit])并配置环境变量
    
    `cd devkithome`

    `ruby dk.rb init`

    `ruby dk.rb install`
* 通过gem安装jekyll。注意:
    1. 此步骤可能由于默认gem资源库被墙需要 [*更换资源*](https://ruby.taobao.org/)
    2. 在jekyll serve 生成本地site时,根据jekyll theme中依赖的jekyll组件不同,可能会报依赖异常,此时只需要gem install xxxx所需依赖即可


## 基于Themes创建个人主页
* 从[Jekyll Themes & Templates](http://jekyllthemes.io/)或者其他资源选择自己喜欢的模板,获取git资源地址。
**eg:** https://github.com/sunykid/sunykid.github.com.git
* 创建本地git仓库**eg:** D:/mygitspace/USERNAME.github.com

    `cd  mygitspace`

    `git  clone  [资源url]  USERNAME.github.com`
* 本地配置修改及预览
    执行`jetyll s/server/serve`即可通过localhost:4000进行本地预览

* 关联本地git与github 可以参见[*廖雪峰的Git教程*](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013752340242354807e192f02a44359908df8a5643103a000)
    
    `cd  USERNAME.github.com`

    `git remote set-url origin https://USERNAME.com/USERNAME/USERNAME.github.com.git`

    `git push`

    此处记不清了，初次添加时需要配置秘钥，配置过程就参见廖大神的博客吧。
    执行完毕访问http://USERNAME.github.io即可访问个人主页

## markdown编写blog
* 通过ruby make插件根据rakefile配置文件,执行rake 命令,eg:*rake post title="mytitle"*。在_post生成.md/.markdown/...文件即可通过编辑器进行编辑了。


[*详细markdown语法可参见*](http://hustlei.tk/2014/08/markdown-basic-syntax.html)

[*详细了解jekyll可参见*](http://jekyllcn.com/)

**本文内容仅提供粗略的步骤罗列，具体细节未再次操作验证，以后不断完善**