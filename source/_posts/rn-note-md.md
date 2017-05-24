---
title: 关于React Native的一点小笔记
date: 2017-01-16 22:47:08
tags: [React Native]

---

之前看`React Native`做的小笔记，算是备忘吧
<!--more-->

## Image
图片的屏幕适配可以通过添加图片中缀`@nx`达到目的，格式如下
`ic_menu.png`
`ic_menu@2x.png`

不同的分辨率对应数字如下

|type|name|
|:--|:--|
|ldpi|0.75|
|mdpi|1|
|htpi|1.5|
|xhdpi|2|
|xxhdip|3|
|xxxhpid|4|

切记要在添加完图片后重启`react-native`服务，不要问我为什么知道

## PropTypes
使用`PropTypes`来强制约束组件需要的参数，使得组件更具有文档性
```javascript
import { PropTypes } from 'react';

ImageItem.propTypes = {
  url: PropTypes.string.isRequired,
};
```
如果调用`ImageItem`组件的时候没有传递`url`属性，则会显示警告或者报错。

枚举类型可以参考[这里](https://facebook.github.io/react/docs/typechecking-with-proptypes.html)

## RN生命周期
主要的方法如下

- componentWillMount
- componentDidMount
- componentWillReceiveProps
- shouldComponentUpdate
- componentWillUpdate
- componentDidUpdate
- componentWillUnmount
- render

>  render方法会多次调用，所以不适合在render方法内做过多的数据操作

> 注意没有componentDidUnmount

> 以上的方法对应到Android的生命周期方法其实是不够用的，所以你可能需要用到Appstate这个小东西。
利用Appstate注册回调，应用切换前后台状态的时候会回调Appstate

## RN布局

|Name|Type|
|:--|:--|
|flexDirection|column \ row \ column-reverse \ row-reverse|
|flexWrap|nowrap \ wrap|
|justifyContent|flex-start \ flex-end \ center \ space-between \ space-around|
|alignItems|streth \ flex-start \ flex-end \ center \ baseline|

以上为布局需要的4个基本属性，第一个参数为其属性的默认值。

> flexWrap 中nowrap表示子元素排列超过屏幕尺寸时不会换行

> justifyContent 定义了子元素在主轴上的对齐方式，而不是单纯的y轴方向
同理，alignItems定义了子元素在主轴的交叉轴上的对齐方式。

> alignItems 中 streth 表示未设置高度时，子元素将在交叉轴上填充父元素，
baseline 表示子元素按第一个元素中文字的基线对齐。

更多的属性参考[这里](http://reactnative.cn/docs/0.40/layout-props.html)

