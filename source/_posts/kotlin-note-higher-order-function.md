---
title: kotlin笔记：高阶函数的使用
date: 2016-12-18 15:57:57
tags: [Android,Kotlin]
categories: Android开发

---

# 前言
学了3个星期的kotlin，并用它做了一个[小项目](https://github.com/pcyan/Gantlin)。
中间遇到了一些小问题，所以就想着写点东西来记录一下，方便日后自己查阅，也可以与他人分享
本篇讲的是kotlin中高阶函数的使用及技巧。
<!-- more -->

# 什么是高阶函数
在kotlin中，变量不仅仅可以是基本类型和引用类型，还可以是一个函数，函数可以被当作参数传递，
或者作为返回值被返回，这样的函数称为高阶函数。
# 举个栗子
当我们使用 `Sqlite`做频繁的数据库操作的时候，有可能会用到事务，我们会需要去调用'beginTransaction()'，然后做数据库操作，成功之后调用'setTransactionSuccessful()'，最后还需要调用'endTransaction()'。
在kotlin中我们可以简化这个操作
定义函数接受开启事务后要做的操作，即一个函数
```kotlin
    fun excuteTransaction(transactionFun:()->Unit){
        val db = dbHelper.writableDatabase
        try{
            db.beginTransaction()
            transactionFun()
            db.setTransactionSuccessful()
        }finally {
            db.endTransaction()
        }
    }
```
然后可以这样使用
```kotlin
    excuteTransaction {
        db.execSQL(...)
        ...
    }
```
是不是简单了很多
>注：在kotlin中，调用函数时，如果参数为函数并且是最后一个参数，可以将参数移到括号外面。移动到括号外面之后如果括号内没有参数，括号可以省略不写。所以最终写法如上。

# 使用扩展函数进一步简化
扩展函数可以使得我们在不需要修改原来类的源代码的基础上，新增我们需要的api
以上面的代码为例，我们可以使它更加简单
```kotlin
    fun SQLiteDatabase.executeTransaction(transactionFun:SQLiteDatabase.()->Unit){
        try{
            beginTransaction()
            transactionFun()
            setTransactionSuccessful()
        }finally {
            endTransaction()
        }
    }
```
定义`executeTransaction()`为`SQLiteDatabase`的扩展函数并使得它接受一个`SQLiteDatabase`的扩展函数,
从而使得`executeTransaction()`以及参数中的函数`transactionFun()`可以使用`this`关键字调用`SQLiteDatabase`的api，进行数据库操作,使用方法如下
```kotlin
    val db = dbHelper.writableDatabase
    db.executeTransaction {
        execSQL(...)
        ...
    }
```
是不是更加简洁明了了，奖励自己一朵小红花

# 放弃麻烦的回调
在Java中如果我们在做完一个操作之后希望能够得到响应并做一些后续的操作，那么我们就会定义一个回调函数
一般我们是这样定义的
```java
interface MyListener{
    void MyCallBack(String str)
}
```
在kotlin中你可以这样调用
```kotlin
//方法一
obj.setMyListener(object:MyListener(){
    fun MyCallBack(str:String){
	...
    }
})
```
就像一般java中的调用一样
然而，kotlin支持像java8中的`SAM转换`(SAM conversion)，意思是当你的接口中之后一个方法的时候
你可以这样调用它
```kotlin
//方法二
obj.setMyListener{str->
    println(str)
        ...
}
```
当你只有一个参数，你甚至可以不写这个参数，使用`it`关键字进行引用
```kotlin
//方法三
obj.setMyListener{
    println(it)
        ...
}
```
看到这里，你可能会觉得那不是挺好的吗，标题说的是什么鬼
我一开始也觉的挺好的，但是当我使用kotlin去定义回调接口而不是使用java的时候
```kotlin
    interface MyListener{
        fun MyCallBack(str:String)
    }
```
上面的接口只能使用**方法一**调用，不能使用方法二，或者三，原因是在kotlin中
`SAM转换`只支持Java的代码，为什么呢？在我思考了`11.6667`秒之后，恍然大~~雾~~悟
kotlin根本就不需要这么麻烦的定义接口回调,你需要的只是一个函数做相应的后续操作，
所以你可以直接这样写
```kotlin
    class Test {

        private var myCallBack: ((str:String) -> Unit)? = null

        fun setMyListener(myCallBack: ((str:String) -> Unit)){
            this.myCallBack = myCallBack
        }
    }

```
这里直接定义一个函数作为变量，作用跟回调一样，调用方法如下

```kotlin
    val test = Test()
    test.setMyListener {
        println(it)
    }
```

#最后
`kotlin`是个好东西！(•̀ᴗ•́)و ̑̑

# 参考资料

[Higher-Order Functions and Lambdas](https://kotlinlang.org/docs/reference/lambdas.html)
[高阶函数和lambda表达式](http://tanfujun.com/kotlin-web-site-cn/docs/reference/lambdas.html)
[号外 号外 Kotlin 1.0 正式发布](http://kotlindoc.com/index.html)
[《Kotlin for android developers》中文版翻译](https://github.com/wangjiegulu/kotlin-for-android-developers-zh)

