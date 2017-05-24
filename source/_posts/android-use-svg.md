---
title: Android 中使用 SVG 的一个坑
date: 2016-06-29 22:44:06
tags: [Android,SVG]
categories: Android开发
---
最近在 Android 中使用的  SVG 比较多，而且 SVG 用起来不是一般的爽，想要改图片大小颜色只要自己改就行了，不用麻烦美工大大。
但是就是在某个天朗气清的早晨，我一如既往的将 SVG 转成 VectorDrawable，然后导入工程使用，发现应用居然
崩溃了，
溃了，
了。
而且只是在部分手机上会崩溃。看了一下日志。

```
java.lang.ArrayIndexOutOfBoundsException: length=32; index=32 at 
android.util.PathParser$PathDataNode.addCommand(PathParser.java:370) at
android.util.PathParser$PathDataNode.nodesToPath(PathParser.java:260) at 
android.graphics.drawable.VectorDrawable$VPath.toPath(VectorDrawable.java:1265) at 
android.graphics.drawable.VectorDrawable$VPathRenderer.drawPath(VectorDrawable.java:950)
```

我的天，我这么心思缜密的程序猿，怎么可能会出现越界这种低级错误。定睛一看，发现错误是出现在VectorDrawable那里。这就奇怪了，一张图片还会越界。没办法，把错误贴到Google上找了一下，终于发现了错误的根源。

>Android 中是不允许使用科学计算符号的，也就是高中数学学到的e

详细见[这里](https://code.google.com/p/android/issues/detail?id=78162)
PS导出SVG的时候为了路径精细会使用科学计算符，类似 `1.05e-4,2.75448`，而低版本 Android  是不能识别的，所以就会发生越界。像这么小的数，我们改成0或者1就可以了，对图片不会产生较大的影响。

好了，异常解决了，又可以愉快的打（ban）代（zhuan）码了。
