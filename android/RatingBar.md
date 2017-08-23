# 自定义 RatingBar 样式

## 可恶的星星
谷歌给的默认样式太大，而且大小不可调。
谷歌还给了两种样式:
```
style="?android:attr/ratingBarStyleSmall"
style="?android:attr/ratingBarStyleIndicator"
```
samll 的样式太小，indicator 不能交互。

## 使用自定义样式

通过指定 android:progressDrawable ，修改 RatingBar 的显示样式。

```xml
<RatingBar
    android:layout_marginLeft="@dimen/text_padding"
    android:layout_marginRight="@dimen/text_padding"
    android:layout_width="wrap_content"
    android:layout_height="45dp"
    android:id="@+id/ratingBar"
    android:progressDrawable="@drawable/rating_color"
    android:numStars="5"
    android:rating="5"
    android:stepSize="1"/>
```

rating_color.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@android:id/background" android:drawable="@drawable/star_grey" />
    <item android:id="@android:id/secondaryProgress" android:drawable="@drawable/star_accent" />
    <item android:id="@android:id/progress" android:drawable="@drawable/star_accent" />
</layer-list>
```
样式搞定了，可是控件的高度没法自适应，只能通过设置 layout_height 控制高度。
