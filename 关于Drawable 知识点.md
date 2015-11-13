#关于Drawable#
 表示一种图像的概念，但又不全是图片，通过颜色也可以构造出各式各样的图片的效果。在实际开发中常被用来作为View的背景使用。Android的设计中，Drawable是一个抽象类，一些子类如ShapeDrawable和BitmapDrawable等。
Drawable的层次关系：
(2)Drawable的内部宽/高这个参数比较重要，通过getIntrinsicWidth和getIntrinsicHeight可以取到，但是并非所有的Drawable都有内部宽/高，一张图片形成的Drawable，他的内部宽高就是图片的宽高，但是一个颜色形成的Drawable就没有内部宽高的概念，另外，Drawable的内部宽高并不等同于他的大小，一般而言，Drawable是没有大小概念的，用作View的背景时，Drawable会被拉伸到和View一样的大小。

##Drawable分类##

### BitmapDrawable###

最简单的Drawable，表示一张图片，开发中，可以直接引用原始的图片即可，也可以通过XML的方式描述他。
### ShapeDrawable###

理解为通过颜色来构造的图形，既可以是纯色的图形，也可以是具有渐变效果的图形，语法稍复杂。
注意标签创建的Drawable，其实体类实际上是GradientDrawable，各个属性的含义如下：

(1)android:shape

表示图形的形状，有四个选项：①rectangle(矩形)②oval(椭圆)③line(横线)④ring(圆环)，默认值是矩形，另外line和ring这两个选项必须要通过标签来指定线的宽度和颜色等信息，否则无法达到预期的效果；

针对ring这个形状，有5个特殊的属性：

①android:innerRadius

圆环的内半径，和android:innerRadiusRatio同时存在时，以android:innerRadius为准；

②android:thickness

圆环的厚度，即外半径去内半径的大小，和android:thicknessRatio同时存在时，以
android:thickness为准；

③android:innerRadiusRatio

内半径占整个Drawable宽度的比例，默认值是9，如果是n，那么内半径=宽度/n；

④android:thicknessRatio

厚度占整个Drawable宽度的比例，默认值是3，如果是n，那么厚度=宽度/n；

⑤android:useLevel

一般应该用false，否则可能达不到预期的效果，除非它被当作LevelListDrawable来使用；

(2)

表示shape的四个角的角度，它只适用于矩形shape，这里的角度是指圆角的程度，用px表示，有如下5个属性；
①android:radius=>为四个角同时设定相同的角度，优先级较低，会被其他四个属性覆盖；
②android:topLeftRadius=>设定最上角的角度；
③android:topRightRadius=>设定右上角的角度；
④android:bottomLeftRadius=>设定左下角的角度；
⑤android:bottomRightRadius=>设定右下角的角度；
(3)

与标签是相互排斥的，solid表示颜色填充，而gradient表示渐变效果，有以下几个属性：
①android:angle

渐变的角度，默认是0,其值必须是45的倍数，0表示从左到右，90表示从上到下，角度会影响渐变的方向；
②android:centerX

渐变的中心点的横坐标；

③android:centerY

渐变的中心点的纵坐标，渐变的中心点会影响渐变的具体效果；

④android:startColor

渐变的起始色；

⑤android:centerColor

渐变的中间色；

⑥android:endColor

渐变的结束色；

⑦android:gradientRadius

渐变半径，仅当android:type="radial"有效

⑧android:useLevel

一般为false，当Drawable作为StateListDrawable使用时为true；

⑨android:tyoe

渐变的类型，有linear(线性渐变)，radial(径向渐变)，sweep(扫描线渐变)三种，默认值是线性渐变；

(4)

表示纯色填充，通过android:color即可指定shape中填充的颜色；

(5)

shape的描边，属性如下：

①android:width

描边的宽度，越大则shape的边缘线就会看起来越粗；

②android:color

描边的颜色；

③android:dashWidth

组成虚线的线段的宽度；

④android:dashGap

组成虚线的线段间的间隔，间隔越大则虚线看起来空隙就越大

注意：如果android:dashWidth和android:dashGap有任何一个为0，那么虚线效果将不能生效；
(6)

这个表示空白，但表示的不是shape的空白而是包含他的View的空白，有四个属性：
①android:left②android:top③android:right④android:bottom
(7)

shape的大小，有两个属性：android:width和android:height,分别表示shape的宽高，表示的是shape的固有大小，但是一般来说他不是shape最终显示的大小。
shape没有宽高的概念，作为View的背景它会自适应View的宽/高。Drawable的两个方法getIntrinsicWidth和getIntrinsicHeight表示的是Drawable的固有宽高，对于某些比如图片的Drawable来说他的固有宽高就是图片的尺寸，对于shape来说，这个时候这两个方法会返回-1，但是如果通过标签来指定宽高信息，这个时候shape就有了所谓的固有宽高，==>总结：标签设置的宽高就是ShapeDrawable的固有宽高，但是作为View的背景时，shape还会被拉伸或者缩小为View的大小。

### LayerDrawable###

LayerDrawable对应的XML标签是,表示的是一种层次化的Drawable集合，通过不同的Drawable放置在不同的层上面从而达到一种叠加后的效果。一个layer-list中可以包含多个item，每个item表示一个Drawable。item的结构也比较简单，常用的属性有
android:top,android:bottom,android:left,android:right,分别表示Drawable相对于View的上下左右的偏移量，单位是像素，另外还可以在android:drawable属性来直接引用一个已有的Drawable资源，也可以在item中自定义Drawable。默认情况下，layer-list中的所有Drawable都会被缩放至View的大小，对于bitmap，需要使用android:gravity属性才能控制图片的显示效果。layer-list有层次的概念，下面的item会覆盖上面的item，通过合理的分层，可以实现一些特殊的叠加效果；

###StateListDrawable

对应于标签，也是表示Drawable集合，每个Drawable对应View的一种状态，这样系统会根据View的状态来选择合适的Drawable。主要用于设置可单击的View的背景，最常见的就是Button。属性如下：
(1)android:constantSize
状态的改变会导致StateListDrawable切换到具体的Drawable，而不同的Drawable具有不同的固有大小。true表示StateListDrawable的固有大小保持不变，这是它的固有大小是内部所有Drawable的固有大小的最大值，false会随着状态的改变而改变，默认是false。

(2)android:dither
是否开启抖动效果，开启这个选项可以让图片在低质量的屏幕上仍然获得较好的显示效果，此选项默认为true。

(3)android:variablePadding

true会随着状态的改变而改变，false表示StateListDrawable的padding是内部所有Drawable的padding的最大值，默认是false，不建议开启。

标签表示一个具体的Drawable，结构较简单，androiod:drawable是一个已有的Drawable资源的id，剩下的属性表示的是View的各种状态，每个item表示的都是一种状态下的Drawable信息，View的常见状态信息如下：

①android:state_presses=>表示按下状态，比如button被按下后仍没有松开的状态
②android:state_focused=>表示View已经获取了焦点

③android:state_selected=>表示用户选择了View

④android:state_checked=>表示用户选中了View，一般适用于CheckBox这类在选中和非选中状态间进行切换的View

⑤android:state_enabled=>表示View当前处于可用状态

默认的item不带状态

 ###LevelListDrawable

对应标签，同样表示一个Drawable集合，集合中的每个Drawable有个level(等级)的概念，根据不同的等级，LevelListDrawable会切换为对应的Drawable，每个item表示一个Drawable，并且有对应的等级范围，由android:minLevel和android:maxLevel指定，在两者间的等级会对应此item中的Drawable。作为View的背景时，可以通过Drawable的setLevel来设置不同的等级从而切换具体的Drawable，如果被用来作为ImageView的前景Drawable，那么还可以通过ImageView的setImageLevel方法来切换Drawable，最后，Drawable的等级是有范围的，0-10000，最小等级是0，是默认值。

### TransitionDrawable

对应于标签，用于实现两个Drawable间的淡入淡出效果，通过他的setTransition和reverseTransition方法来实现淡入淡出的效果以及他的逆过程

### InsetDrawable

对应于标签，可以将其他的Drawable内嵌到自己中，并可以在四周留出间距，当一个View希望自己的背景比自己的实际区域小时可以用InsetDrawable实现，其实通过LayerDrawable也可以实现这种效果。

### ScaleDrawable

对应于标签，可以根据自己的等级(level)将指定的Drawable缩放到一定比例。

### ClipDrawable

对应于标签，可以根据自己的等级(level)来裁剪另一个Drawable，裁剪方法可以通过android:clipOrientation和android:gravity共同控制。

###自定义Drawable

Drawable使用范围较单一，一个是作为ImageView中的图像来显示，另外就是作为View的背景，大多数是作为View的背景出现的。Drawable的工作原理较简单，核心就是draw方法，系统会调用Drawable的draw方法来绘制View的背景，那么重写Drawable的draw方法就可以自定义Drawable了。
通常不会去自定义Drawable，因为自定义的Drawable无法在xml中使用。