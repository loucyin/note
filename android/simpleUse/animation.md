## 动画类型
### Property Animation

### View Animation

### Drawable Animation

## 简单使用
### Activity 跳转动画设置
在 style 中找到 Activity 使用的 Theme 添加：
```xml
<item name="android:windowAnimationStyle" >@style/slide</item>
```

```xml
<style name="slide" parent="@android:style/Animation">
    <item name="android:activityOpenEnterAnimation">@anim/slide_right_in</item>
    <item name="android:activityOpenExitAnimation">@anim/slide_left_out</item>
    <item name="android:activityCloseEnterAnimation">@anim/slide_left_in</item>
    <item name="android:activityCloseExitAnimation">@anim/slide_right_out</item>
</style>
```

### Fragment 的切换动画
```java
private void showFilterCar() {
    if(mFragment == null){
        mFragment = new SelectCarFragment();
        mFragment.setOnCancelOrFinishListener(new SelectCarFragment.OnCancelOrFinishListener() {
            @Override
            public void onCancel() {
                hideFilterCar();
            }

            @Override
            public void onFinish() {
                hideFilterCar();
            }
        });
    }
    mFilterShow = true;
    FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();
    transaction.setCustomAnimations(R.anim.show_bottom_in,R.anim.hide_bottom_out);
    transaction.replace(R.id.flContainer,mFragment);
    transaction.commit();
}
private void hideFilterCar(){
    mFilterShow = false;
    FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();
    transaction.setCustomAnimations(R.anim.show_bottom_in,R.anim.hide_bottom_out);
    transaction.remove(mFragment);
    transaction.commit();
}
```

### View 的显示隐藏动画
```java
private void showSearchBar(){
    mTvShowSearch.setText("取消");
    mSearchView.setVisibility(View.VISIBLE);
    Animation animation = AnimationUtils.loadAnimation(this,R.anim.show_top_in);
    mSearchView.startAnimation(animation);
}
private void hideSearchBar(){
    mTvShowSearch.setText("搜索");
    Animation animation = AnimationUtils.loadAnimation(this,R.anim.hide_top_out);
    mSearchView.startAnimation(animation);
    mSearchView.setVisibility(View.GONE);
}
```

## 参考链接
- [Animation and Graphics](https://developer.android.com/guide/topics/graphics/index.html)
- [Animation Resources](https://developer.android.com/guide/topics/resources/animation-resource.html#Property)
