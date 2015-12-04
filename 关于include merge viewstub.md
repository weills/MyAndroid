##关于include merge viewstub##
##include##

.........

       <include layout="@layout/....." />  
 灵活复写include 注意外层布局的嵌套,注意id
##merge##
       <merge xmlns:android="http://schemas.android.com/apk/res/android">  
       ......没有外层根布局，
       </merge> 

  再去include 。是include的辅助类，它的主要作用是为了防止在引用布局文件时产生多余的布局嵌套。优化布局 优化性能    
##viewstub##
  仅在需要时才加载布局
  和View的可见性设置 功能上没太大差别，主要是可见性设置，每个元素还拥有着自己的宽、高、背景等等属性，解析布局的时候也会将这些隐藏的元素一一解析出来，而Viewstub是需要再加载，也就是对性能的影响，通常还要设置点击事件等等，，个人感觉没多大用，需要时百度下吧
