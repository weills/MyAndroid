##关于setContentView##


###理解java反射，和xml被显示相关##
###window,view,activity相关（仔细看安卓开发艺术探索相关章节） ##

1. Window是一个抽象类，提供了绘制窗口的一组通用API。

2. PhoneWindow是Window的具体继承实现类。而且该类内部包含了一个DecorView对象，该DectorView对象是所有应用窗口(Activity界面)的根View。

3. DecorView是PhoneWindow的内部类，是FrameLayout的子类，是对FrameLayout进行功能的修饰（所以叫DecorXXX），是所有应用窗口的根View 。


###PhoneWindow类的setContentView(int layoutResID)方法源码###
     


##Activity的setContentView##




----------------------------------------------------------
1. 创建一个DecorView的对象mDecor，该mDecor对象将作为整个应用窗口的根视图。

2. 依据Feature等style theme创建不同的窗口修饰布局文件，并且通过findViewById获取Activity布局文件该存放的地方（窗口修饰布局文件中id为content的FrameLayout）。

3. 将Activity的布局文件添加至id为content的FrameLayout内。
4. 当启动Activity调运完ActivityThread的main方法之后，接着调用ActivityThread类performLaunchActivity来创建要启动的Activity组件，在创建Activity组件的过程中，还会为该Activity组件创建窗口对象和视图对象；接着Activity组件创建完成之后，通过调用ActivityThread类的handleResumeActivity将它激活。




##关于VIEW的启动过程##
安卓开发艺术探索第九章








	


