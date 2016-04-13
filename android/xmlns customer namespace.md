# 自定义namespace
1. 创建attrs.xml  
在value文件夹下新建attrs.xml
- 创建一个 declare-styleable元素  
```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <declare-styleable name="CircleView">
        <attr name="radius" format="float"/>
        <attr name="circleColor" format="color|reference"/>
    </declare-styleable>
</resources>
```
- attr标签属性  
attr 有两个属性，format属性用于定义值的类型，boolean,integer,float,string不需要解释。
reference:资源ID，color:颜色值，dimension:尺寸值，fraction:百分数，enum:枚举值，
flag:位或运算。  
format可以指定值为多种类型，比如说circleColor可以是color，也可以是reference。


# 在自定义View中使用
- 首先，自定义一个View,构造函数如下：  
```java
public CircleView(Context context, AttributeSet attrs, int defStyleAttr)
    {
        super(context, attrs, defStyleAttr);
        TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.CircleImageView);
        for (int i = 0; i < typedArray.getIndexCount(); i++) {
            int attr = typedArray.getIndex(i);
            switch (attr)
            {
                case R.styleable.CircleView_circleColor:
                    mColor = typedArray.getColor(attr,0xffbbbbbb);
                    break;
                case R.styleable.CircleView_radius:
                    mRadius = typedArray.getInt(attr,10);
                    break;
            }
        }
        typedArray.recycle();
    }
```
- 在布局文件中使用CircleView
```xml
<com.demo.CircleView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:circleColor="#ff0000"
        app:radius="15"/>
```


参考链接：  
[ Android中attrs.xml文件的使用详解](http://blog.csdn.net/jiangwei0910410003/article/details/17006087)  
[Android开发学习之TypedArray类](http://blog.csdn.net/richerg85/article/details/11749421)  
[TypedArray Reference](http://developer.android.com/reference/android/content/res/TypedArray.html)  
[关于CoordinatorLayout与Behavior的一点分析](http://www.jianshu.com/p/a506ee4afecb)
