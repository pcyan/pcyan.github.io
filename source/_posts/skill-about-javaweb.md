---
title: JavaWeb 开发中的一些小技巧
date: 2017-04-22 15:38:41
tags: [Java,Web,Maven,Mybatis]
categories: [Java]
---

主要梳理一下最近在学习JavaWeb开发中会用到的一些小技巧
<!--more-->

## Maven

### 镜像地址
默认的镜像地址是`Maven Central`，在国内访问的话会比较慢，所以我用`Nexus`在本地搭建一个私有的`Maven`仓库，如何搭建私有仓库在[上一篇博客](/Java/use-nexus-to-create-private-maven-repo/)也有提到。
这样做可以提高速度，避免重复下载依赖。
当然，不想搭建私有仓库的话也可以直接使用`Ali`的仓库地址.
要让`Maven`使用自定义的仓库地址可以修改`Maven`的配置文件`conf/setting.xml`
```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
...
  <localRepository>F:/maven_repository</localRepository>
...
  <mirrors>
     <mirror>
        <id>nexus</id>
        <mirrorOf>*</mirrorOf>
        <name>private repository</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      </mirror>
  </mirrors>
...
</settings>
```
之后注意在开发工具中修改Maven的配置文件地址就可以了。

### Maven命令
`Maven Install` 会将模块编译到本地的Maven仓库中。所以可以提供本地模块的相互依赖。


## Mybatis Generator
Mybatis的原理就不细说了，主要通过`Mapper`去操纵数据库。
每次都去编写对数据库的一些基本操作的话都是一些机械的模板的工作。
所以他本身就有提供一个`mybatis-generator`的插件以自动生成`Mapper`文件。i
这里主要讲如何在Intellij中使用这个插件生成`Mybatis`模板
### 添加插件
要使用这个插件，首先要在`pom.xml`中添加相应的配置
```xml

</project>
...
    <build>
        <plugins>
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.3.2</version>
                <configuration>
                    <verbose>true</verbose>
                    <overwrite>true</overwrite>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

### 插件的配置
添加插件的配置文件`generatorConfig.xml`，指定对应的数据库表和要生成的文件的位置，格式。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>

    <!--导入属性配置 -->
    <properties resource="jdbc.properties"/>

    <!-- 数据库驱动-->
    <classPathEntry location="${driverLocation}"/>

    <context id="DB2Tables" targetRuntime="MyBatis3">
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <!-- 是否去除自动生成的注释 true：是 ： false:否 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!--数据库链接URL，用户名、密码 -->
        <jdbcConnection driverClass="${driver}" connectionURL="${url}"
                        userId="${username}" password="${password}">
        </jdbcConnection>

        <!-- 非必需，类型处理器，在数据库类型和java类型之间的转换控制-->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- 生成模型的包名和位置-->
        <javaModelGenerator targetPackage="io.pcyan.webdemo.pojo" targetProject="src/main/java">
            <property name="enableSubPackages" value="false"/>
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!-- 生成映射文件的包名和位置-->
        <sqlMapGenerator targetPackage="io.pcyan.webdemo.mapping" targetProject="src/main/java">
            <property name="enableSubPackages" value=""/>
        </sqlMapGenerator>

        <!-- 生成DAO的包名和位置-->
        <javaClientGenerator type="XMLMAPPER" targetPackage="io.pcyan.webdemo.dao" targetProject="src/main/java">
            <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>

        <!-- 要生成的表 tableName是数据库中的表名或视图名 domainObjectName是实体类名-->
        <table tableName="user_t" domainObjectName="User" enableCountByExample="false"
               enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false"
               selectByExampleQueryId="false"/>

        <table tableName="teacher_t" domainObjectName="Teacher" enableCountByExample="false"
               enableUpdateByExample="false" enableDeleteByExample="false" enableSelectByExample="false"
               selectByExampleQueryId="false"/>
    </context>
</generatorConfiguration>
```

其中的`jdbc.properties`为数据库的配置文件，在这里就不放了。

### 使用插件
点击`Edit Configuration`，添加一个`Maven`配置，在`Paramaters->Command Line`写入

```shell
mybatis-generator:generate -e
```
保存后，选择generator的运行配置点击运行就可以生成`mybatis`的模板。

## Intellij 中xml的构建问题
Maven配置的Java web项目，默认会将编译好的文件放到项目的target文件夹下面，但是使用Intellij编译项目后，`src/main/java`文件夹下的`*.xml`是不会编译到`target`文件夹中的，所以如果Mybatis的`*Mapper.xml`和`*Mapper.java`放在一起的话，会在编译后找不到。
用Eclipse编译项目的话，就不会发生这种情况。
解决这中问题可以有两种方法。

第一种是将所以`xml`文件都放在`resources`文件夹下，编译后所有`xml`文件都可以在`target`文件夹下原封不动的找到。
这种方法比较适用于新的项目，但是与`Java`文件分离了，不适合源代码的管理。同时旧的项目也不方便把所有的`*.xml`文件都进行移动。

第二种方法就是在`pom.xml`中进行配置，使Intellij在编译时会包含`xml`文件
```xml
<build>
        <resources>
            <resource>
                <!-- This include everything else under src/main/java directory -->
                <directory>${basedir}/src/main/java</directory>
                <excludes>
                    <exclude>**/*.java</exclude>
                </excludes>
            </resource>
        </resources>
...
</build>
```
