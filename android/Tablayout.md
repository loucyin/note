## 依赖
```
compile 'com.android.support:design:23.4.0'
```
## xml 使用
```xml
<android.support.design.widget.TabLayout
    android:id="@+id/tabLayout"
    android:background="@color/white"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:tabIndicatorColor="@color/main_accent"
    app:tabSelectedTextColor="@color/main_accent"
    app:tabTextAppearance="@android:style/TextAppearance.DeviceDefault.Small"/>

    <android.support.v4.view.ViewPager
    android:overScrollMode="never"
    android:layout_marginTop="@dimen/divider_size"
    android:id="@+id/viewPager"
    android:layout_width="match_parent"
    android:layout_height="0dp"
    android:layout_weight="1"/>
```
## 代码中绑定 ViewPager
```java
mAdapter = new SelectPagerAdapter(getActivity());
mViewPager.setAdapter(mAdapter);
mTabLayout.setupWithViewPager(mViewPager);
```
需要重写 Adapter 的 getPageTitle 方法
```java
@Override
public CharSequence getPageTitle(int position) {
    return mTitles==null?""+position:mTitles.get(position);
}
```

## 参考链接
- [android design library提供的TabLayout的用法](https://segmentfault.com/a/1190000003500271)
- [TabLayout](https://developer.android.com/reference/android/support/design/widget/TabLayout.html#attr_android.support.design:tabGravity)
