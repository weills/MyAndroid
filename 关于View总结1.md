#关于View1#
##*《Android开发艺术探索》读书笔记*##

##View的参数##
**1.top,left,right,bottom** 是相对父容器坐标
通常按第四象限坐标轴定义

     width=right-left     
     height=bottom-top
     Left=getLeft()
     Right=getRight()
     .....

    
##关于View的事件##
1.
MotionEvent提供了两组方法：getX/getY和getRawX/getRawY，getX/getY返回的是相对于当前View左上角的x和y坐标，而getRawX/getRawY返回的是相对于手机屏幕左上角的x和y坐标；

2.
TouchSlop是系统所能识别出的被认为是滑动的最小距离，如果两次滑动之间的距离小于这个常量，那么系统就不认为你是在进行滑动操作

        ViewConfiguration.get(getContext)).getScaledTouchSlop()。
用此可获取

3.
VelocityTracker追踪手指在滑动中的速度，包括水平和竖直方向的速度；viewpager可以玩玩，设置时间间隔

4.GesturceDetector用于辅助检测用户的单击、滑动、长按、双击等行为；

3.Scroller
弹性滑动对象
用它来实现有过渡效果的滑动

##关于View的滑动##
1.ScrollBy和ScrollTo，简单归纳下： ScrollBy的0点在一般情况下均为可见的top那条线，有一种特殊情况就是（书中未提及）当某个ViewGroup在内部的layout的时候设置margin为负值的View时，0点会在可见top上方的高度（或宽度）为margin的地方，这个鬼东西实在是太绕口，具体的参考blog，参考headerView的设置。竖向滑动时，上滑ScrollY不断增加（所以应该传正值），下滑时ScrollY不断减少（所以应该传负值）；同理，横向滑动时，左滑ScrollX不断增加，右滑不断减少。 ScrollTo可以理解为把View滑动到ScrollX或ScrollY为指定值的位置


2.动画：注意View动画的View移动只是位置移动，其本身还是在原来位置，会导致一些bug。


3.通过LayoutParams，有点烦 不过这个参数很重要
##事件分发##
 这个写的有点啰嗦，要写写的一大框 再加上滑动冲突，具体在自定义View里面用自己的话说说吧，单独写写总结下，被坑过，很重要。

##View工作原理##

###1.三大流程以及常见的回调方法（构造方法，onAttach,OnVisibilityChanged,OnDetach等）###

###2.ViewRoot和DecorView###
ViewRoot对应ViewRootImpl类，它是连接WindowManager和DecorView的纽带，View的三大流程均通过ViewRoot来完成。

ActivityThread中，Activity创建完成后，会将DecorView添加到Window中，同时创建ViewRootImpl对象，并建立两者的关联。

View的绘制流程从ViewRoot的performTraversals方法开始，经过measure、layout和draw三大流程。
###关于Measuresec###
1.就是32位INT 值，高2位代表SpecMode 低30位代表 specSize，位运算不要忘了啊！！！！

2.三类SpecMode   UNSPECIFIED 这个基本不要管 EXACTLY 精确大小或matchparent
  AT_MOST，warp_content(自定义VIEW 或viewGROUP时 略 烦！)

3.子View和父容器的MeasureSpec关系归纳：


a. 子View为精确宽高，无论父容器的MeasureSpec，子View的MeasureSpec都为精确值且遵循LayoutParams中的值。


b.子View为match-parent时，如果父容器是精确模式，则子View也为精确模式且为父容器的剩余空间大小；如果父容器是wrap-content，则子View也是wrap-content且不会超过父容器的剩余空间。

c. 子View为wrap_content时，无论父View是精确还是wrap-content，子View的模式总是wrap-content，且不会超过父容器的剩余空间。

##view工作流程##
##view自定义##
*不好归纳 在自定义VIew里面试着总结下吧*








