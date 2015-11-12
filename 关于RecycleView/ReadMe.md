##关于RecycleView##
###support-v7###
1.Appcompat

2.CardView

3.GridLayout

4.MediaRouter

5.Palette

7.RecyclerView


###RecyclerView提供这些内置的布局管理器:###

	* LinearLayoutManager 示出了在垂直或水平滚动列表项。
	* GridLayoutManager 网格展示的布局。
	* StaggeredGridLayoutManager 交错网格展示。




.RecyclerView.ItemAnimator类 和其动画有关

##基本使用##

mRecyclerView = findView(R.id.id_recyclerview);
//设置布局管理器
mRecyclerView.setLayoutManager(layout);
//设置adapter
mRecyclerView.setAdapter(adapter)
//设置Item增加、移除动画
mRecyclerView.setItemAnimator(new DefaultItemAnimator());
//添加分割线
mRecyclerView.addItemDecoration(new DividerItemDecoration(
                getActivity(), DividerItemDecoration.HORIZONTAL_LIST));

##LayoutManager##
LinearLayoutManager 现行管理器，支持横向、纵向。
GridLayoutManager 网格布局管理器
StaggeredGridLayoutManager 瀑布就式布局管理器

##Click and LongClick##
1.mRecyclerView.addOnItemTouchListener去监听然后去判断手势


2.通过adapter中自己去提供回调


eg:2.

     //... class HomeAdapter extends RecyclerView.Adapter<HomeAdapter.MyViewHolder>
      {


    public interface OnItemClickLitener
    {
        void onItemClick(View view, int position);
        void onItemLongClick(View view , int position);
    }

    private OnItemClickLitener mOnItemClickLitener;

    public void setOnItemClickLitener(OnItemClickLitener mOnItemClickLitener)
    {
        this.mOnItemClickLitener = mOnItemClickLitener;
    }

    @Override
    public void onBindViewHolder(final MyViewHolder holder, final int position)
    {
        holder.tv.setText(mDatas.get(position));

        // 如果设置了回调，则设置点击事件
        if (mOnItemClickLitener != null)
        {
            holder.itemView.setOnClickListener(new OnClickListener()
            {
                @Override
                public void onClick(View v)
                {
                    int pos = holder.getLayoutPosition();
                    mOnItemClickLitener.onItemClick(holder.itemView, pos);
                }
            });

            holder.itemView.setOnLongClickListener(new OnLongClickListener()
            {
                @Override
                public boolean onLongClick(View v)
                {
                    int pos = holder.getLayoutPosition();
                    mOnItemClickLitener.onItemLongClick(holder.itemView, pos);
                    return false;
                }
            });
        }
    }

    }
//...

adapter中自己定义了个接口，然后在onBindViewHolder中去为holder.itemView去设置相应 
的监听最后回调我们设置的监听。

最后别忘了给item添加一个drawable:

     <?xml version="1.0" encoding="utf-8"?>
    <selector xmlns:android="http://schemas.android.com/apk/res/android" >
    <item android:state_pressed="true" android:drawable="@color/color_item_press"></item>
    <item android:drawable="@color/color_item_normal"></item>
    </selector>

Activity中去设置监听：


        mAdapter.setOnItemClickLitener(new OnItemClickLitener()
        {

            @Override
            public void onItemClick(View view, int position)
            {
                Toast.makeText(HomeActivity.this, position + " click",
                        Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onItemLongClick(View view, int position)
            {
                Toast.makeText(HomeActivity.this, position + " long click",
                        Toast.LENGTH_SHORT).show();
                        mAdapter.removeData(position);
            }
        });




##自动加载##

      recyclerView.setOnScrollListener(new RecyclerView.OnScrollListener() {
             @Override
             public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
                super.onScrolled(recyclerView, dx, dy);
                  int lastVisibleItem = ((LinearLayoutManager) mLayoutManager).findLastVisibleItemPosition();
                  int totalItemCount = mLayoutManager.getItemCount();
                //lastVisibleItem >= totalItemCount - 4 表示剩下4个item自动加载，各位自由选择
                // dy>0 表示向下滑动
                if (lastVisibleItem >= totalItemCount - 4 && dy > 0) {
                    if(isLoadingMore){
                         Log.d(TAG,"ignore manually update!");
                    } else{
                         loadPage();//这里多线程也要手动控制isLoadingMore
                        isLoadingMore = false;
                    }
                }
            }
        });

如果想用GridView，可以试试这个，注意例子里的span_count =2

          @Override
           public void onScrolled(RecyclerView recyclerView, int dx, int dy) 
           { 
             super.onScrolled(recyclerView, dx, dy);
             int[] visibleItems = mLayoutManager.findLastVisibleItemPositions(null); int lastitem = Math.max(visibleItems[0],visibleItems[1]);
             Log.d(TAG,"visibleItems =" + visibleItems); 
             Log.d(TAG,"lastitem =" + lastitem);
             Log.d(TAG,"adapter.getItemCount() =" + adapter.getItemCount());
              if (dy > 0 && lastitem > adapter.getItemCount() - 5 && !isLoadingMore)
                 { 
                  Log.d(TAG,"will loadNewFeeds");
                 }
             }