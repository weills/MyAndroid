#关于View总结2#


##*《Android开发艺术探索》读书笔记*##


##view工作流程##

###measure过程###
1.view的measure过程

  View的measure过程由其measure方法来完成，measure方法是个final方法，子类不能重写，在View的measure方法中会调onMeasure方法，分析View的onMeasure方法


1，一个比较好的习惯是在onLayout方法中去获取View的测量宽高或最终宽高。
2.如何在Activity初始化时获取View的宽高：


 a. Activity或者View的onWindowFocusChanged方法（注意该方法会在Activity Pause和resume时被多次调用）。


b. view.post(new Runnable( {@Overiddepublic void run(){})});在run方法中获取。


c. ViewTreeObserver中的onGlobalLayoutListener中。
d.略
  

2.viewGroup的measure过程

对ViewGroup来说，除了要完成自己的measure过程外，还要遍历去调用所有子元素的measure方法，各个子元素再递归去执行这个过程。不同于View，ViewGroup是一个抽象类，没有重写View的onMeasure方法，但是提供了一个measureChildren的方法 

###layout 过程###
layout的作用是viewgroup用来确定子元素的位置，当ViewGrou的位置被确定后，它在onLayout中会遍历所有的子元素并调用其layout方法，在Layout方法确定View本身的位置

*layout中可能会使view的大小大于测量的大小*



##draw的过程##


绘制背景（background.draw(canvas)），绘制自己（onDraw()），绘制chidren（dispatchDraw），绘制装饰（onDrawScrollBars）


##自定义View##


1.自定义View的几种分类

2.注意点

a  直接继承View或ViewGroup的需要自己处理wrap_content。


b View要在onDraw方法中要处理padding，而ViewGroup要在onMeasure和onLayout中处理padding和margin。


c View中的post方法可以取代handler。


d 在View的onDetachedFromWindow中停止动画，线程或回收其他资源。

f 滑动冲突处理。


##事件分发##
   自定义View大多数都要处理滑动冲突 （容器类控件尤甚） 滑动冲突的解决原理 来自于事件分发机制 这个很重要

 三个方法 
 dispatchTouchEvent
 onInterceptTouchEvent
 onTouchEvent


  三大方法关系的伪代码


    public boolean dispatchTouchEvent(MotionEvent ev) { 
    boolean consume = false;
    if(onInterceptTouchEvent(ev)) { 
        consume = onTouchEvent(ev);
    } else { 
        consume = child.dispatchTouchEvent(ev); 
    }
    return consume; 
    }

  简直 一目了然

如果当前View拦截事件，就交给自己的onTouchEvent去处理，否则就丢给子View继续走相同的流程。


事件传递顺序：Activity -> Window -> View，如果View都不处理，最终将由Activity的onTouchEvent处理。
##关于滑动冲突##

弄懂事件分发 
外部拦截 内部拦截（本质都一样）
 



##自定义View的思想##







重剑无锋 大巧不工 此乃内功，关于View的弹性滑动 滑动冲突 绘制原理 达到一定境界 方能不滞于物 游刃有余 




