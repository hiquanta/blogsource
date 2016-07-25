---
title: VelocityTracker,GestureDetector和Scroller
date: 2016-07-20 21:33:43
tags: Android
---
1. VelocityTracker

 用于追踪手指滑动过程中的速度

 使用过程:

 * 首先,在View的onTouchEvent方法中追踪当前单击事件的速度:

```java
VelocityTracker velocityTracker=VelocityTracker.obtain();
velocityTracker.addMovement(event);
```
  * 接着,当我们想知道当前的滑动速度时，这个时候可以采用如下方式来获得当前的速度:
```java
VelocityTracker.computeCurrentVelocity(1000);
int xVelocity=(int)velocityTracker.getXVelocity();
int yVelocity=(int)velocityTracker.getYVelocity();
```
注意三点:

第一点,获取速度之前必须先计算速度,即getXVelocity和getYVelocity这两个方法的前面必须要调用computeCurrentVelocity方法;

第二点:这里的速度是指一段时间内手指所滑过的像素数,

第三点:速度可以为负数,当手指从右往左滑动时,水平方向速度即为负值.
速度的计算公式:
速度=(终点位置-起点位置)/时间段
通过公式和Android坐标系,可知道,手指逆着坐标系的正方向滑动,所产生的速度就为负值.
computeCurrentVelocity这个方法的参数表示的是一个时间单元或者说时间间隔,它的单位是毫秒(ms),计算速度时得到的速度就是在这个时间间隔内手指在水平或竖直方向上所滑动的像素数.
 * 最后,当不需要使用它的时候,需要调用clear方法来重置并回收内存:
 ```java
 velocityTracker.clear();
velocityTracker.recycle();
 ```

2. GestureDetector
 使用过程:

 * 首先,需要创建一个GestureDetector对象并实现OnGestureListener接口,和实现OnDoubleTapListener监听双击行为:
 ```java
 GestureDetector mGestureDetector=new GestureDetector(this);
//解决长按屏幕后无法拖动的现象
mGestureDetector.setIsLongpressEnabled(false);
 ```
 * 接着,接管目标View的onTouchEvent方法,在待监听View的onTouchEvent方法中添加如下实现:
 ```java
 boolean consume=mGestureDetector.onTouchEvent(event);
return consume;
 ```

 * 然后,有选择地实现OnGestureListener和OnDoubleTapListener中的方法:onSingleTapUp(单击),onFling(快速滑动),onScroll(拖动),onLongPress(长按)和onDoubleTap(双击).
 用于手势检测,检测用户的点击、滑动、长按、双击等行为。



如果只是监听滑动相关的,在onTouchEvent中实现,如果要监听双击这种行为的话,那么就使用GestureDetector.

[测试代码](https://github.com/hiquanta/masterandroid)

3. Scroller
 弹性滑动对象，用于实现View的弹性滑动
## 使用方法
```java
Scroller scroller=new Scroller(mContext);
//缓慢滑动到指定位置
private void smoothScrollTo(int destX,int destY){
    int scrollX=getScrollX();
    int delta=destX-scrollX;
    //1000ms内滑向destX,效果就是慢慢滑动
    mScroller.startScroll(scrollX,0,delta,0,1000);
    invalidate();
}
@Override
public void computeScroll(){
    if(mScroller.computeScrollOffset()){
    scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
    postInvalidate();
    }
}
```



## 相关文章
[手势事件：滑动动速度跟踪类VelocityTracker介绍](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2012/1117/574.html)

[Android View系列一: View基础知识](http://blog.csdn.net/fang323619/article/details/51367158)

[ 用户手势检测-GestureDetector使用详解](http://blog.csdn.net/harvic880925/article/details/39520901)

[ Android中滑屏初探 ---- scrollTo 以及 scrollBy方法使用说明](http://blog.csdn.net/qinjuning/article/details/7247126)
[ Android中滑屏实现----手把手教你如何实现触摸滑屏以及Scroller类详解](http://blog.csdn.net/qinjuning/article/details/7419207)
