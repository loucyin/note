# ScrollView 嵌套 RecyclerView

## 问题

设置 RecyclerView 的高度为 wrap_content ,但是 RecyclerView 的高度不能自适应

## 解决办法

使用 NestedScrollView 代替 ScrollView，这样做滑动不流畅。

```java
LinearLayoutManager manager = new LinearLayoutManager(getActivity()){
    @Override
    public boolean canScrollVertically() {
        return false;
    }
};
```

简单粗暴的方法，在 LayoutManager 中禁止滑动。

## 参考链接

- [RecyclerView inside ScrollView is not working](http://stackoverflow.com/questions/27083091/recyclerview-inside-scrollview-is-not-working)
- [解决NestedScrollView 嵌套 RecyclerView出现的滑动冲突问题](http://blog.csdn.net/yaochangliang159/article/details/50540276)
- [ScrollView嵌套RecyclerView时滑动出现的卡顿](http://zhanglu0574.blog.163.com/blog/static/113651073201641853532259/)
