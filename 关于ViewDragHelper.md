##关于ViewDragHelper##
-关于手势处理相关（注意与GestureDetector的区别），

drawerLayout就是基于此来实现，

对于自定义View很是有用


##关于使用

    public class VDHLayout extends LinearLayout
    {
    private ViewDragHelper mDragger;

    public VDHLayout(Context context, AttributeSet attrs)
    {
        super(context, attrs);
        mDragger = ViewDragHelper.create(this, 1.0f, new ViewDragHelper.Callback()
        {
            @Override
            public boolean tryCaptureView(View child, int pointerId)
            {
                return true;
            }

            @Override
            public int clampViewPositionHorizontal(View child, int left, int dx)
            {
                return left;
            }

            @Override
            public int clampViewPositionVertical(View child, int top, int dy)
            {
                return top;
            }
        });
    }

    @Override
    public boolean onInterceptTouchEvent(MotionEvent event)
    {
        return mDragger.shouldInterceptTouchEvent(event);
    }

    @Override
    public boolean onTouchEvent(MotionEvent event)
    {
        mDragger.processTouchEvent(event);
        return true;
    }




ViewDragHelper. 其中1.0f是敏感度参数参数越大越敏感。第一个参数为this，表示该类生成的对象，他是ViewDragHelper的拖动处理对象，必须为ViewGroup。 第三个参数就是Callback，在用户的触摸过程中会回调相关方法.



要让ViewDragHelper能够处理拖动需要将触摸事件传递给ViewDragHelper，这点和gesturedetector是一样的. onInterceptTouchEvent中通过使用mDragger.shouldInterceptTouchEvent(event)来决定我们是否应该拦截当前的事件。 onTouchEvent中通过mDragger.processTouchEvent(event)处理事件。

ViewDragHelper中拦截和处理事件时，需要会回调CallBack中的很多方法来决定一些事，比如：哪些子View可以移动、对个移动的View的边界的控制等等。



##关于回调函数##

tryCaptureView如何返回ture则表示可以捕获该view，你可以根据传入的第一个view参数决定哪些可以捕获

clampViewPositionHorizontal,clampViewPositionVertical可以在该方法中对child移动的边界进行控制， left , top 分别为即将移动到的位置，比如横向的情况下，我希望只在ViewGroup的内部移动，即：最小>=paddingleft， 最大<=ViewGroup.getWidth()-paddingright-child.getWidth,就可以按照如下代码编写：


           @Override
            public int clampViewPositionHorizontal(View child, int left, int dx)
            {
                final int leftBound = getPaddingLeft();
                final int rightBound = getWidth() - mDragView.getWidth() - leftBound;

                final int newLeft = Math.min(Math.max(left, leftBound), rightBound);

                return newLeft;
            }


