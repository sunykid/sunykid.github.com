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


# 依赖管理基础

* 7.1 什么事依赖管理
* 7.2 声明依赖
* 7.3 依赖配置
* 7.4 外部依赖
* 7.5 仓库
* 7.6 发布组件
* 7.7 Where to next？

## 7.1什么是依赖管理（Dependency Management）


粗略的讲，依赖管理有两部分组成。一是Gradle得知道并找到它构建和运行时需要哪些东西。我们称这些引入的文件为项目依赖。二是Gradle能够构建和上传项目生产出来的东西。我们称这些输出的文件为项目发布。下面详细了解一下：

大多数的项目并不是完全独立的。它们在编译或者测试等等操作时，需要提供由其他项目生成的文件。就像我们在项目中用到Hibernate时，在编译时需要在classPath中引入一些Hibernate的jar包。在运行测试时，我可能还需要在classpath下添加其他额外的jars，比方说一个指定的Jdbc 驱动包，或者Ehcache 包。

这些引入的文件构成了项目的依赖。Gradle允许你告知这些项目依赖，以方便Gradle查找他们并在构建中使用用它们。这些依赖可能从远程的Maven仓库或者Ivy仓库,也可能是本地文件，还可能是多项目构建中的一个子项目。我们称这一过程称为**依赖解析**。

相比ant的优势概述(略)

通常这些依赖本身还会有其他依赖，例如Hibernate core运行时需要在classpath中加入一些其他的libraries。在测试你的项目时，Gradle需要找到这些依赖的依赖并使他们可用。我们称这一过程为**依赖传递**。

另外的，项目中的输出文件构成了项目的发布。Gradle替你维护这一重要工作。你只需要在项目中做好发布声明，然后gradle就可以根据你想做的在任何地方构建和发布他们。我们称这一过程为**发布**。

## 7.2声明依赖

**例7.1声明依赖**

**build.gradle**

{% highlight gradle %}
    apply plugin: 'java'

    repositories {
        mavenCentral()
    }

    dependencies {
        compile group: 'org.hibernate', name: 'hibernate-core', version: '3.6.7.Final'
        testCompile group: 'junit', name: 'junit', version: '4.+'
    }
{% endhighlight %}

这段脚本说明了
1. 编译此项目时需要Hibernate core 3.6.7.Final也就是说运行时Hibernate core的依赖也是必须的。
2. 在编译项目的测试代码时需要junit>=4.0
3. Gradle在Maven 中央仓库查找这些依赖。

## 7.3依赖配置

Gradle中依赖被归集到多个配置项中。一个配置项简单来说就是一个命名了的依赖集合。我们把他么称作**依赖配置**。你可用用他们声明外部依赖或者你发布的项目。

Java 插件中定义了许多标准的配置项。这些配置项代表了Java plugin在classpaths中使用的依赖。(很别扭原文贴上：These configurations represent the classpaths that the Java plugin uses.)

下面列举一些：

[**See更多**](https://docs.gradle.org/current/userguide/java_plugin.html#tab:configurations)

* **compile**:编译生产环境源代码需要的依赖。
* **runtime**:生产环境下classes运行时需要的依赖。包含编译时依赖。
* **testCompile**:编译测试代码的依赖，默认包含被编译的生产换将classes及编译时依赖。
* **testRuntime**：运行测试时需要的依赖，默认包含**compile**，**runtime**及**test compile**依赖。

你可以定义自己的configurations以便在构建中适用。参见[**see Section 23.3, “Dependency configurations”**](https://docs.gradle.org/current/userguide/dependency_management.html#sub:configurations)

## 7.4外部依赖

可以声明多种依赖，外部依赖是其中一种。外部依赖是依赖当前构建以外
构建的文件并且被存储在一种仓库中，比如Maven中央仓库，Maven企业仓库，Ivy仓库或者一个本地文件系统中的一个目录。

**例7.2 定义外部依赖**

**build.gradle**
{% highlight gradle%}
dependencies {
    compile group: 'org.hibernate', name: 'hibernate-core', version: '3.6.7.Final'
}
{% endhighlight %}
个人理解(groovy语法),具体查看gradle Api：

    dependencies({
        compile([group:'org.hibernate',name: 'hibernate-core', version: '3.6.7.Final'])
    })

一个外部依赖由group,name,version属性指定。根据使用的仓库种类不同group,version是可选的。
声明外部依赖的简写格式:“group:name:version”

**例7.3**

**build.gradle**
dependencies {
    compile 'org.hibernate:hibernate-core:3.6.7.Final'
}

想知道更多定义和使用dependencies信息。参见[ Section 23.4, “How to declare your dependencies”](https://docs.gradle.org/current/userguide/dependency_management.html#sec:how_to_declare_your_dependencies)

## 7.5 Repositories仓库
Gradle在repository(仓库)中查找外部依赖。一个仓库是许多由group,name,version定义了的文件的集合.Gradle识别多种不同的仓库格式，如Maven、Ivy及多种访问仓库的方式，如本地文件系统或者HTTP。
**例7.4 使用maven 中心仓库**

**build.gradle**

    repositories {
        mavenCentral()
    }

或者Bintray's JCenter:

**例7.5 使用JCenter仓库**

**build.gradle**

    repositories {
        jcenter()
    }

或者任意的Maven远程仓库

**例7.6 使用Maven远程仓库**

**build.gradle**

    repositories {
        maven {
            url "http://repo.mycompany.com/maven2"
        }
    }

或者远程Ivy目录

**例7.7 使用Ivy远程仓库**

**build.gradle**

    repositories {
        ivy {
            url "http://repo.mycompany.com/repo"
        }
    }
 
你可以使用maven或者Ivy的本地仓库。

**例7.8 使用Ivy本地仓库**

**build.gradle**

    repositories {
        ivy {
            // URL can refer to a local directory
            url "../local-repo"
        }
    }

一个项目中可以有多个不同的仓库。Gradle将会按照它们被指定的顺序依次查找某个依赖。并在发现第一个包含被请求的模块的仓库时停止查找。    

## 7.6发布组件


**例7.9 发布到Ivy仓库**

**build.gradle**

    uploadArchives {
        repositories {
            ivy {
                credentials {
                    username "username"
                    password "pw"
                }
                url "http://repo.mycompany.com"
            }
        }
    }

当你执行**gradle uploadArchives** ,gralde就会构建并且上传你的Jar，并且会生成并且上传ivy.xml

也可以发布到Maven仓库。语法稍有不同，注意发布到Maven仓库你需要使用Maven插件。Gradle也会自动生成并上传pom.xml.

**例7.10 发布到maven仓库**
**build.gradle**

    apply plugin: 'maven'

    uploadArchives {
        repositories {
            mavenDeployer {
                repository(url: "file://localhost/tmp/myRepo/")
            }
        }
    }

## 7.7 Where to next?
查看所有的依赖解析细节，查看 [Chapter 23, Dependency Management](https://docs.gradle.org/current/userguide/dependency_management.html)
组件发布[Chapter 30, Publishing artifacts](https://docs.gradle.org/current/userguide/artifact_management.html)

如果你对提到的DSL元素感兴趣，请查看

[Project.configurations{}](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:configurations(groovy.lang.Closure))
[ Project.repositories{}](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:repositories(groovy.lang.Closure))
[Project.dependencies{}](https://docs.gradle.org/current/dsl/org.gradle.api.Project.html#org.gradle.api.Project:dependencies(groovy.lang.Closure))
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

一点需要注意的是，如果你用的是Gradle wrapper，第一种方式需要你明确指定wrapper脚本的路径，如果 你不在项目的根路径下，比方说 你再webservice项目下，你就应该执行 **../../gradlew build**


# 9.持续构建


## 9.1 如何启动和停止持续构建

1. 通过添加 --continuous 或者 -t。
2. 通过 CTRL-D终止(在Windows环境下，需要在输入CTRL-D后按ENTER或RETURN键--说实话不知道这个RETURN键是啥，命令行提示可以通过输入exit退出 )

## 9.2 怎样才能触发构建过程

只有任务执行时的输入信息发生变化时才会被关注。Gradle只有在任务启动的时候才会监视变化。其他的变化不会引发构建过程。比如：改变脚本内容和脚本 逻辑将不会引发构建。类似的，改变在配置阶段而不是执行阶段读取的文件也不会引发构建。如果要体现这些变化，必须要重新启动构建过程。

按照通常的目录设计，考虑一个典型构建过程。下图通过图表将**gradle build**形象化了：

![](https://docs.gradle.org/current/userguide/img/javaPluginTasks.png)

下面是图表中的关键任务及其相应的文件输入目录

**compileJava**

   src/main/java

**processResources**

   src/main/resources

**compileTestJava**

   src/test/java

**processTestResources**

   src/test/resources

假设最初的构建是成功的。
1. 改变上述文件内容或者增加删除文件都会引发新的构建
2. Gradle的增量构建能够确保只有真正受影响的任务才会被执行。
3. 如果改变java source(src/main/java)时发生了编译错误，那么随后在src/test/java中的变化将不会引发新的构建。因为test源代码依赖main源代码。直到在main源码中修复这个编译错误。
4. 只有真正被执行了的任务，它的inputs才会被监控。

## 9.3 局限和怪癖
**暂略**