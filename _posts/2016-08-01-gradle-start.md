---
layout: post
title: "gradle-start"
description: 
---
#用gradle构建工程

##gradle安装

##关于gradleWrapper

##识别项目特征
* 多项目构建

#为一个工程创建新的构建脚本


##快速创建工程
gradle init --type=java-library //创建工程骨架

##gradle一些核心概念

* [Gradle构建阶段](https://docs.gradle.org/current/userguide/build_lifecycle.html#sec:build_phases)
    1. 初始化
        gradle支持单一项目和组合项目的构建。在初始化过程中，Gradle确定哪些项目将参与构建，然后为这些项目分别创建项目实例。 
    2. 配置
        在这一阶段项目对象配置完成，这次构建中的所有项目的构建脚本在此阶段执行。Gradle1.4中 引入了一个可选择的特性，叫做 “configuration on demand”（后台配置）。使用这种模式，Gradle只会配置相关项目see[*Section 24.1.1.1, “Configuration on demand”*](https://docs.gradle.org/current/userguide/multi_project_builds.html#sec:configuration_on_demand)。
    3. 执行
        Gradle确定哪些在配置阶段创建和配置的Tasks的子集将被执行。这些子集由当前目录和传递给gradle命令的参数确定。然后Gradle分别执行这些被选定的任务。

        * [The project API and properties](https://docs.gradle.org/current/userguide/writing_build_scripts.html?_ga=1.16470450.501591023.1470100081)