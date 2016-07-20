---
title: Android触控之MotionEvent
date: 2016-07-20 16:40:12
tags: Android
---
<!--more-->
[MotionEvent](https://developer.android.com/reference/android/view/MotionEvent.html)

MotionEvent里定义了一系列的手势事件
常见事件类型：

| 参数     | 解释     |
| :------------- | :------------- |
| ACTION_DOWN       | 表示用户开始触摸.       |
| ACTION_MOVE       | 表示用户在移动(手指或者其他)       |
| ACTION_UP       | 表示用户抬起了手指       |

一次手指触摸屏幕的行为会触发一系列点击事件，考虑一下几种情况

* 点击屏幕后离开，事件顺序为：DOWN->UP
* 点击屏幕滑动一会儿再松开，事件顺序为：DOWN->MOVE->...>MOVE->UP


## 更多参考
[android触控,先了解MotionEvent(一)](http://my.oschina.net/banxi/blog/56421)

TouchSlop()

TouchSlop是系统所能识别出的被认为是滑动的最小距离，换个说法，当手指在屏幕上滑动时，如果两次滑动之间的距离小于这个 常量，那么系统就不认为你是在进行滑动操作。
 原因间之：滑动的距离太短，系统不认为它是滑动的。这是一个常量，和设备有关，在不同设备上这个值可能是不同的，
通过如下方式即可获取这个常量

```java
 ViewConfiguration.get(getContext()).getScaledTouchSlop();
 ```
 > 这个常量有什么意义呢？

 当我们在处理滑动时，可以利用这个常量来做一些过滤, 比如当两次滑动事件的滑动距离小于这个值，我们就可以认为未达到滑动距离的临界值，因此就可以认为它们不是滑动，这样做可以有更好的用户体验。

如果细看的话，可以在源码中找到这个常量的定义，在frameworks/base/core/res/res/values/config.xml文件中。

```xml
<!-- Base "touch slop"  value used by ViewConfiguration as a movement threshold where scrolling should begin .-->

<dimen name ="config_viewConfigurationTouchSlop">8dp</dimen>
```
