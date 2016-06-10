## 姿势不对，导致的坑
### 使用百度地图定位，只能定位一次
>在application标签中声明service组件,每个app拥有自己单独的定位service
```xml
<service android:name="com.baidu.location.f" android:enabled="true" android:process=":remote">
</service>
```

### 百度地图的 Search
使用完一定要销毁，如果不销毁，关闭 Activity ，在使用 MapView 的时候地图操作会很卡。

### 地图的生命周期
要在 Activity 的生命周期中，添加 MapView 的生命周期处理，否则会出现灵异现象。
