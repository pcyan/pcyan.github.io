---
title: 搭建Maven私有仓库
date: 2017-04-08 18:02:30
tags: [Maven]
categories: [Java]

---

# 前言
最近发现多个项目中有引用相同的模块，所以想着怎么解耦公共模块，就找到了私有仓库这条路。
后来发现私有仓库还可以建代理仓库，通过代理将依赖下载到代理仓库后下次可以直接从代理仓库拉取(≖ ‿ ≖)✧，
突然发现Java团队开发真的太需要私有仓库了。
这里使用的是`Nexus`搭建私有仓库（我也不知道有没有其他工具可以搭建），关于`Nexus`的更详细的介绍，可以自行搜索。
<!--more-->

# 搭建私有仓库

## 安装Nexus
下载[Nexus Repository Manager OSS 2.x - bundle.zip](https://www.sonatype.com/download-oss-sonatype)
解压进入`nexus-2.14.3-02-bundle\nexus-2.14.3-02\bin\jsw`，选择相应的操作系统的文件夹，我是`windows`，
所以选择了`nexus-2.14.3-02-bundle\nexus-2.14.3-02\bin\jsw\windows-x86-64`，双击`console-nexus.bat`即可运行。

## 配置
运行成功后可以通过[http://127.0.0.1:8081/nexus/](http://127.0.0.1:8081/nexus/)打开管理页面，点击左边的`Repostories`可以管理仓库

![over_view](over_view.PNG)

想要管理仓库需要先登录，默认的用户名密码是

|username|password|
|:--|:--|
|admin|admin123|

默认存在的仓库我们可以不管，首先要做的是添加一个阿里的代理仓库，因为`Jcenter`和`MavenCentral`真的太慢了，必须要配置一个国内的仓库地址。
点击 `Add-->ProxyRepository` 即可添加代理仓库
阿里的Maven仓库地址为[http://maven.aliyun.com/nexus/content/groups/public/](http://maven.aliyun.com/nexus/content/groups/public/)
注意重写本地存储目录，每次从代理仓库下载的依赖会下载到指定的目录。
其他配置选项也不是特别重要，可以自行去搜索，之后点保存即可。

创建完代理仓库可以接着创建私有仓库，私有仓库可以存放公司的二方库。
点击 `Add-->Hosted Repository` 添加私有仓库，配置方式与代理仓库类似。

所以现在我们有了两个仓库，一个代理仓库`Ali`和一个私有仓库`pcyan`，为了便于客户端使用，我们可以使用`Group Repository`来管理多个仓库，这里我直接使用了`Public Repositories`
点击`Public Repositories --> Configuration`
将我们的两个仓库移动到左边，其他用不到的移动到右边


![public_repo_config](public_repo_config.PNG)

保存之后，我们就可以使用`Public Repositories`的链接来使用这两个库了。

# 如何使用
## 上传仓库
创建Library Module blah blah blah，比如说我的叫做`testutil`
在`testutil`的`build.gradle`中加入如下配置

```java
apply plugin: 'com.android.library'
apply plugin: 'maven'

def libVerName = '1.0'
def libVerCode = 1

android {
    compileSdkVersion 24
    buildToolsVersion "25.0.0"

    defaultConfig {
        minSdkVersion 16
        targetSdkVersion 24
        versionCode libVerCode
        versionName libVerName
    ...
    }
    ...
}

dependencies {
   ...
}
uploadArchives {
    configuration = configurations.archives
    repositories {
        mavenDeployer {
            // 私有仓库的地址，以及账户
            repository(url: 'http://127.0.0.1:8081/nexus/content/repositories/pcyan/') {
                authentication(userName: 'admin', password: 'admin123')
            }
            // Library的配置
            pom.project {
                version libVerName
                artifactId 'testutil'
                groupId 'pcyan.util'
                packaging 'aar'
                description 'test util to say hello'
            }
        }
    }
}
```

之后点击`Android Studio`右边的`Gradle-->testutil-->upload-->uploadArchives`就可以上传仓库了
>注意，上传完后，我们的仓库地址为`groupId:artifactId:version `也就是`pcyan.util:testutil:1.0`


## 下载依赖
通过私有仓库下载依赖只需要在项目根目录的`build.gradle`中配置maven地址即可
>注意：默认的Android Studio 项目会使用jcentger的仓库地址，需要注释掉，只使用自己的仓库地址

```java
buildscript {

    repositories {
        // Public Repositories 的地址
        maven{ url 'http://127.0.0.1:8081/nexus/content/groups/public/' }
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.2'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}


allprojects {
    repositories {
        // Public Repositories 的地址
        maven{ url 'http://127.0.0.1:8081/nexus/content/groups/public/' }
    }
}
```


# 最后
用的开心 (•̀ᴗ•́)و ̑̑
