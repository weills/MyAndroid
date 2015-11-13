
RecyclerView实现下拉刷新和上拉加载更多以及瀑布流效果


# 使用方法
build.gradle文件
```java
dependencies {
  compile 'com.wuxiaolong.pullloadmorerecyclerview:library:1.0.1'
}
```
设置线性布局
```java
 mPullLoadMoreRecyclerView = (PullLoadMoreRecyclerView) view.findViewById(R.id.pullLoadMoreRecyclerView);
 mPullLoadMoreRecyclerView.setLinearLayout();
```
设置网格布局
```java
 mPullLoadMoreRecyclerView.setGridLayout(2);//参数为列数
```
设置交错网格布局，即瀑布流效果
```java
 mPullLoadMoreRecyclerView.setStaggeredGridLayout(2);//参数为列数
```
下拉刷新
```java
mPullLoadMoreRecyclerView.setRefreshing(true);
```
刷新结束
```java
mPullLoadMoreRecyclerView.setPullLoadMoreCompleted();
```
