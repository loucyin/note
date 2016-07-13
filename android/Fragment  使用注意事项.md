# ViewPager 中使用 Fragment

## 问题描述

在 ViewPager 中使用 Fragment ，并且 Fragment 存在请求网络逻辑的时候。在切换 Fragment 的过程中会出现多次请求网络的现象。

## 解决方法

将 Presenter 的初始化，以及请求网络的逻辑放到 onCreate 中，将视图的初始化放到 onViewCreated 中，在请求数据成功后的处理方法中，不要使用 View 控件，使用的时候要判断是否非空。

```java
@Override
public void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mPresenter = new NewsPresenter(this);
    mPresenter.load();
}
@Override
    public void onViewCreated(View view, Bundle savedInstanceState) {
      super.onViewCreated(view, savedInstanceState);
      mRecyclerView.setLayoutManager(new LinearLayoutManager(getActivity()));
      mRecyclerView.setPullRefreshEnabled(false);

      if(mAdapter == null) {
          initRvAdapter(mList);
      }
      mRecyclerView.setAdapter(mAdapter);
}
```
