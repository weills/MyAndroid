#关于Activity全面分析#
1.当用户打开新的Activity或者切换回桌面时，回掉如下：
onPause>>onStop 
**不过新Activity采用透明主题，那么当前Activity不会调用onStop**


2.当用户再次回到原Activity时 回掉如下 onRestart>>onStart>>onResume

3.Back 键 onPause>> stop>> Destroy




##异常情况##

Activity在异常情况下被回收时，onSaveInstanceState方法会被回调，回调时机是在onStop之前，当Activity被重新创建的时 候，onRestoreInstanceState方法会被回调，时序在onStart之后；

1.系统配置改变

     android:configChanges="orientation"等等

2.资源内存不足

      不同的优先级
##启动模式##
1.standard 系统默认。每次启动会重新创建新的实例，谁启动了这个Activity，这个Activity就在谁的栈里。

2.singleTop 栈顶复用模式。该Activity的onNewIntent方法会被回调，onCreate和onStart并不会被调用。


3.singleTask 栈内复用模式。只要该Activity在一个栈中存在，都不会重新创建，onNewIntent会被回调。如果不存在，系统会先寻找是否存在需要的栈，如果不存在该栈，就创建一个任务栈，然后把这个Activity放进去；如果存在，就会创建到已经存在的这个栈中。


4.singleInstance
单实例模式，是一种加强的singleTask


**注意**:①在singleTask，Activity所需的任务栈很重要，那么，什么是它所需要的任务栈呢？
一个叫TaskAffinity的参数，可以指定任务栈的名字，默认情况下，所有Activity所需的任务栈的名字是应用的包名，这个属性要和singleTask启动模式或者allowTaskReparenting属性配对使用，在其他情况下没有意义。
②任务栈分为前台任务栈和后台任务栈，后台任务栈中的Activity处于暂停状态
除了在Manifest中给activity标签设置launchMode属性外就是给intent加Flags了，调intent.addFlags(),后者的优先级较高，另外就在限定范围不同，第一种无法直接为Activity设定FLAG_ACTIVITY_CLEAR_TOP标识，第二种无法为Activity指定singleInstance模式


##IntentFilter匹配规则##
1.action匹配规则：要求intent中的action 存在 且 必须和过滤规则中的其中一个相同 区分大小写；


2.category匹配规则：系统会默认加上一个android.intent.category.DEAFAULT，所以intent中可以不存在category，但如果存在就必须匹配其中一个；


3 data匹配规则：data由两部分组成，mimeType和URI，要求和action相似。如果没有指定URI，URI但默认值为content和file（schema）