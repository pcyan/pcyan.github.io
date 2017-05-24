---
title: 为RecyclerView点击添加Ripple波纹效果
date: 2016-03-06 22:54:08
tags: [Android,Ripple]
categories: Android开发

---
MD的特色之一就是"有意义的动画效果"，让动画符合物理世界的规律，而不是杂乱无章，华而不实。
点击事件产生的波纹效果就是其中之一。
<!--more-->
那么，先让我们看一下什么是波纹效果：


![touch_feedback.gif](touch_feedback.gif)

>可以看出，点击变得更具有质感而不是简单的使条目变色。

让我们在RecyclerView上做下实验
RecyclerView默认点击是没有任何效果的

![touch1.gif](touch1.gif)

MD之前，我们为条目添加点击效果是这样的
新建`drawable/touch_bg.xml`
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@color/super_light_grey" android:state_pressed="true"/>
    <item android:drawable="@color/super_light_grey" android:state_focused="true"/>
    <item android:drawable="@color/white"/>
</selector>
```

然后为点击的条目设置`android:background="@drawable/touch_bg"`
在点击条目的时候便可以看到条目变色

![touch2.gif](touch2.gif)

但是我们现在想要的是波纹效果，**这里要注意，波纹效果只在5.0以上的设备生效**
所以我们需要新建`drawable-v21/touch_bg.xml`
```
<?xml version="1.0" encoding="utf-8"?>
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
        android:color="@color/line_grey">
    <item android:drawable="@color/white"/>
</ripple>
```
之后，便可以看到期待的波纹效果。

![touch3.gif](touch3.gif)

代码以及第一张图片均参考项目[Animate](https://github.com/hitherejoe/animate)
希望能够对你有所帮助
