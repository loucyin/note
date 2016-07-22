## 代码实现

```java
overridePendingTransition(android.R.anim.slide_in_left,android.R.anim.slide_out_right);
```
## activity 跳转动画
```xml
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>

        <item name="android:windowAnimationStyle" >@style/slide</item>
    </style>

    <style name="slide" parent="@android:style/Animation.Activity">
        <item name="android:activityOpenEnterAnimation">@android:anim/slide_in_left</item>
        <item name="android:activityOpenExitAnimation">@android:anim/slide_out_right</item>
        <item name="android:activityCloseEnterAnimation">@android:anim/slide_in_left</item>
        <item name="android:activityCloseExitAnimation">@android:anim/slide_out_right</item>
    </style>

</resources>
```
## 参考链接
- [android仿微信的activity平滑水平切换动画](http://www.cnblogs.com/Jaylong/archive/2012/08/30/activity.html)
