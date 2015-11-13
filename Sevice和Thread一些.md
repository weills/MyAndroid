##Service和Thread一些##
1. 在Service里也要创建一个子线程，那为什么不直接在Activity里创建呢？这是因为Activity很难对Thread进行控制，当Activity被销毁之后，就没有任何其它的办法可以再重新获取到之前创建的子线程的实例。而且在一个Activity中创建的子线程，另一个Activity无法对其进行操作。但是Service就不同了，所有的Activity都可以与Service进行关联，然后可以很方便地操作其中的方法，即使Activity被销毁了，之后只要重新与Service建立关联，就又能够获取到原有的Service中Binder的实例。因此，使用Service来处理后台任务，Activity就可以放心地finish，完全不需要担心无法对后台任务进行控制的情况。


2.**service的onbind**
用于和Activity建立关联

##关于IntentService##
很有用，特别注意，

##前台Service##
创建Notification对象
调用了它的setLatestEventInfo()
startForeground（）

##远程Service##
难用 和IPC 有关 ，不好总结，在IPC 中




