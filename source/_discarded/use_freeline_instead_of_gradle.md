---
title: 替代Gradle的构建工具--Freeline
date: 2016-09-04 23:15:51
tags: [Android,Gradle,Freeline]
categories: Android开发

---
废话不多说，先奉上 [Freeline Github主页](https://github.com/alibaba/freeline)
`Freeline` 是阿里的一个团队开发的 `Android` 项目构建工具。使用 `Freeline` 的话，
在经过全量编译（第一次全新的编译）后，之后的每次编译都可以通过增量编译，只提交更改部分的内容，所以编译速度非常快。
<!-- more -->
## 实战体验
这里对于 `Feeline` 的具体用法我就不说了，项目主页已经说的很详细了。
实际体验过后我发现，在开启了 `MultiDex` 的项目中，`Freeline` 的速度也是要比 `Gradle` 快一倍的。
拿我们公司的项目来说(具体什么项目就不表了)，我是没有固态硬盘，`win7`的环境，项目是开启了 `MultiDex`，module 有5+ 。
编译速度如下

|/|	Gradle|	Freeline|
|:--|:--|:--|
|全量编译(max)|	4min+|	100s+|
|全量编译(min)	|3min+	|70s+|
|增量编译/InstantRun	|> 10s|	< 5s|
关于 `Gradle` 的构建速度有点记不清了，反正是开了 `InstnatRun` 之后也没有感觉快很多.
而且 `Freeline` 与 `InstantRun` 相比，在 `Freeline` 下修改 `Java` 文件或者资源，都是可以增量并且都是在 5s 内的。
`Gradle` 的话修改资源可以跑 `InstnatRun`，但是一旦修改了资源文件，又需要重新构建，也就是全量编译，又需要花费大量的时间。
所以这段时间体验下来，`Freeline` 确实帮我节省了很多时间，可以更加专注的开（tou）发（lan）。

## 附件
下面贴一下我的 `Freeline` 在 `Gradle` 的配置，如果你也想用 `Freeline` 的话，这可能对你有帮助

```
apply plugin: 'com.android.application'
apply plugin: 'com.antfortune.freeline'
android {
    freeline {
        hack true
        productFlavor 'dev'
        apkPath project.rootProject.file("D:\\workspace\\my_project\\app\\build\\outputs\\apk").getAbsolutePath()+'\\app-dev-debug-'+parent.ext.versionName+'.apk'
        #如果是Linux可以使用相对路径，win没试过相对路径
        #apkPath project.rootProject.file("app/build/outputs/apk").getAbsolutePath()+'/app-dev-debug-'+parent.ext.versionName+'.apk'
    }
    ...
    productFlavors {
        //instant run
        dev{
            minSdkVersion  21
        }
        
        ...
        productFlavors.all { flavor ->
            flavor.manifestPlaceholders = [UMENG_CHANNEL_VALUE: name]
            flavor.minSdkVersion 14
        }
    }
    //apk添加版本号
    android.applicationVariants.all { variant ->
        def file = variant.outputs[0].outputFile
        variant.outputs[0].outputFile = new File(file.parent, file.name.replace(".apk", "-" + defaultConfig.versionName + ".apk"))
    }
    sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
    }
}
dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    testCompile 'junit:junit:4.12'
    
    ...
    compile 'com.antfortune.freeline:runtime:0.5.4'
    
}
```
## 最后
`Freeline` 开源出来还不久，所以还不能完全的符合日常的开发需求。
如果你有任何的问题，可以到 [项目主页](https://github.com/alibaba/freeline) 提 issue。

最后的最后，能够当一名开发者，了解到这么有趣的东西，真的是太好了。
