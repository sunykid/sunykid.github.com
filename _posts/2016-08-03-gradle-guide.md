---
layout: post
title: "gradle学习"
description: 
---
# About Gradle


# Working with existing builds


    
## 4.使用Gradle命令行

    
### 4.1执行多个任务

你可以在执行命令时列出多个tasks，从而在一次构建中执行多个tasks。eg：
**gradle compile test** 将会执行 compile 和 test 任务.Gradle将会按照任务在命令行中出现的顺序依次执行,同时还会执行每个任务中依赖的其他任务.不管一个任务是在命令行中指定的还是被其他的任务依赖，或是两者兼而有之，每个任务在一次build中只执行一次。
一起看个例子：
下面有四个Task。dist 任务 和 test任务 都依赖compile 任务，
运行 **gradle dist test** 结果compile任务只执行了一次。

**图4.1**

![](https://docs.gradle.org/current/userguide/img/commandLineTutorialTasks.png)
**4.1中执行的多个任务**

**build.gradle**

    {% highlight gradle %}
    task compile << {
        println 'compiling source'
    }

    task compileTest(dependsOn: compile) << {
        println 'compiling unit tests'
    }  

    task test(dependsOn: [compile, compileTest]) << {
        println 'running unit tests'
    }  

    task dist(dependsOn: [compile, test]) << {
        println 'building the distribution'
    }  
    {% endhighlight %}

**gradle dist test**输出

    > gradle dist test
    :compile
    compiling source
    :compileTest
    compiling unit tests
    :test
    running unit tests
    :dist
    building the distribution

    BUILD SUCCESSFUL

    Total time: 1 secs

每一个任务仅仅被执行一次，所以 **gradle test test** 与 **gradle test**完全相同。

## 4.2排除任务


你可以通过在命令行中添加 **-x** 来排除制定名称的任务。我们通过上面的构建脚本进行尝试。

**4.2 排除任务例子**

**gradle dist -x test** 的输出

    > gradle dist -x test
    :compile
    compiling source
    :dist
    building the distribution

    BUILD SUCCESSFUL

    Total time: 1 secs

从这个例子可以发现test任务没有执行，尽管它被dist所依赖。你还会注意到test任务的依赖像compileTest也没有执行。但是test的这些依赖中如果被其他的任务需要像 compile，还是会执行。

## 4.3出现一个失败后继续构建

默认情况下，Gradle构建过程中遇到任何一个任务失败，都会马上终止构建过程 。这样能够使构建更快结束，但是会隐藏那些本应该发生的其他失败。为了在一次构建中发现尽可能多的失败，你可以适用 --continue 选项。
当通过 --continue 执行时，Gradle会执行每一个被依赖的任务，而不会在遇到第一个失败的时候停止构建。每一个被遇到的错误会在构建完成后报告。
如果一个任务执行失败，接下来依赖它的所有任务都不会被执行。例如：当test中存在一个编译错误的时候，test将不会执行，因为test任务依赖编译任务（可能是直接依赖或间接依赖）。

## 4.4任务名称缩写

当你在命令行中指定任务的时候，并不须填写全名。你仅仅提供任务名称的一部分，只要能够唯一识别这个任务就可以。

**将4.3中的任务名称缩写**
**gradle di** 的输出

    > gradle di
    :compile
    compiling source
    :compileTest
    compiling unit tests
    :test
    running unit tests
    :dist
    building the distribution

    BUILD SUCCESSFUL

    Total time: 1 secs

你可以对以驼峰方式命名的名称进行缩写。例如：
gradle compTest 可以写成 gradle cT

**例4.4 对驼峰缩写**

**gradle cT** 输出

    > gradle cT
    :compile
    compiling source
    :compileTest
    compiling unit tests

    BUILD SUCCESSFUL

    Total time: 1 secs

##4.5 执行指定构建脚本

当执行**gradle**命令的时候，会默认在当前目录下寻找构建脚本。你可以通过 -b 选项执行其他的构建脚本。如果使用-b了选项settings.gradle就不会被用到。

**例4.5 指定脚本**

**subdir/myproject.gradle**
 {% highlight gradle %}
task hello << {
    println "using build file '$buildFile.name' in '$buildFile.parentFile.name'."
}
 {% endhighlight %}

**gradle -q -b subdir/myproject.gradle hello** 输出　

    > gradle -q -b subdir/myproject.gradle hello
    using build file 'myproject.gradle' in 'subdir'.

**-q (quite 方式执行 仅输出错误日志)**

另外一种选择是，你可以通过 -p 选项指定适用的项目目录。对于multi-project
构建，你应当使用 -p 而不是 -b 选项。

**例4.6 选择指定目录作为项目目录**

**gradle -q -p subdir hello** 输出

    > gradle -q -p subdir hello
    using build file 'build.gradle' in 'subdir'.

##4.6 强制任务执行
许多任务，特别是gradle内建的任务支持，增量构建。这些任务能够确定他们是否需要执行。换句话说，就是知道他们的输入(inputs)和输出(outputs)从上一次执行后
是否发生了变化。你能够很容易的识别出增量任务，因为在构建时，他们会在名字旁边显示"UP-TO-DATE"字样.
你可以通过选择使用 --rerun-tasks 选项忽略 up-to-date检查，来执行所有的任务。看例子：(参照这个例子我没有执行出效果，不知什么原因不起作用,稍后再查查原因吧)

**例4.7 强制任务执行**

 **gradle doIt**输出

     > gradle doIt
    :doIt UP-TO-DATE

**gradle --rerun-tasks doIt** 输出

    > gradle --rerun-tasks doIt
    :doIt

注意这种方式会强制所有需要的tasks被执行，并不仅仅是你再命令行中指定的这些。它有一点像执行clean命令，但是没有输出删除信息。

## 获取构建信息


## 4.7


## 4.8


## 4.9


# 8 multi-project构建介绍


## 8.1 multi-project构建的结构
这种构建方式的共同特征：
1. setting.gradle文件————项目根目录
2. build.gradle文件————项目根目录
3. 包含各自*.gradle 构建脚本的字目录（一些multi-project炫目可能会省略字项目中的构建脚本）

setting.gradle 告诉Gradle项目是怎么样组织的。你还可以通过gradle projects命令查看。
下面是通过该命令输出的java Gradle samples的结构：

**例8.1 列出构建中的项目**

**gradle -q projects** 输出

    > gradle -q projects

    ------------------------------------------------------------
    Root project
    ------------------------------------------------------------

    Root project 'multiproject'
    +--- Project ':api'
    +--- Project ':services'
    |    +--- Project ':services:shared'
    |    \--- Project ':services:webservice'
    \--- Project ':shared'

    To see a list of the tasks of a project, run gradle <project-path>:tasks
    For example, try running gradle :api:tasks


**setting.gradle 配置子项目**
{% highlight gradle %}
include "shared", "api", "services:webservice", "services:shared"
{% endhighlight %}

默认情况下，Gradle使用setting.gradle所在的目录名称作为root项目的名称，这种情况在像Jenkins的持续集成服务中，目录名称可能是通过该自动生成的，跟VCS中的名称不匹配。所以，建议总是设置root project的名称（包括单项目 single project）。

**通过在setting文件中设置 rootProject.name=??设置**

注意 :

1. 每一个项目通常都会有一个build文件，但并不是必须的。但是root project必须要有一个。
2. root build.gradle 通常用来共享一些子项目中通用的配置，如果喜欢的话，还可以把所有的配置信息配置到一个单一的子项目中。这就意味着每一次都需要查看root build才能知道子项目是怎么配置的。
3. 另外需要记住的是构建脚本可能并不叫作 build.gradle.许多项目把它命名为项目名称。


## 8.2 执行一个multi-project build


你有两种方式选择运行哪个项目的任务：

* 切换到你感兴趣的子项目相应的目录中区并且执行  **gradle task**
* 使用一种可以从任何目录访问的项目名称描述，不过通常从根项目目录执行。例如
* **gradle :services:webservice:build** 将会构建webservice子项目及其他所有被它依赖的子项目。

第一种方式的**gradle test**将会执行所有相对于当前工作目录中的子项目的test任务。

