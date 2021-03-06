##Android中的IPC机制##
概念：IPC=>Inter-Process Communication,是进程间通信，也就是两个进程间进行数据交换

注意：IPC并非Android特有的，Binder才是,各个OS平台都有自己的IPC机制，Android基于linux但是并没有照搬linux的IPC机制。

应用场景：多进程，分两种：①单个应用采用多进程，每个模块需要运行在单独的进程中或者为了获取多份内存空间；②当前应用需要向其他应用获取数据，其实之前用的ContentProvider就是跨进程通信的一种方式，具体可以了解下ContentProvider的底层实现。

##1.Android中的多进程模式##

开启多线程模式的方法：在Manifest中给四大组件的android:process属性指定，默认的进程名是包名

特殊情况(非常规)：通过JNI在native层去fork一个新的进程

指定的方式不同，以进程名":"开头的进程是当前应用的私有进程，其他的是全局的，Android系统会给每个应用配一个唯一的UID，只有UID相同的应用才能共享数据

##2.多进程模式的运行机制##

2.1 使用多进程带来的几个问题
(1)静态成员和单例模式完全失效；
(2)线程同步机制完全失效；
(3)SharedPreferences的可靠性会下降；
(4)Application会多次重建；
原因：Android系统给不同的进程或者应用分配了单独的虚拟机，内存分配上有不同的地址空间，导致了不同的虚拟机中访问同一个类的对象会有多个副本，会创建各自的Application

##3.IPC基础概念##

###3.1 Servialzable接口###
java提供的一个序列化接口
Android中提供的新的序列化接口就是Parcelable接口
通过Servialzable接口实现序列化的方式：①实现Servialzable接口；②声明一个serialVersionUID(非必须)

### 3.2 Parcelable接口###
### 3.3 Binder###

##4 Android中的IPC方式##

4.1 使用Bundle


4.2 使用文件共享

4.3 使用Messenger

4.4 使用AIDL
4.5 使用ContentProvider

##5 Binder连接池##

##6 选用合适的IPC方式##
总结 *三个字，有点难* 