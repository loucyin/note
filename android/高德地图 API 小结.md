##  和百度地图一样的坑
### 只能定位一次
定位第一次成功，定位第二次蓝屏，我去这是什么鬼。  
没有注册定位 Service ，导致只能定位一次，第二次返回默认的地址 0，0 ，这是一片汪洋大海，也难怪会蓝屏了。
```xml
<service android:name="com.amap.api.location.APSService"/>
```

## 获取不到导航对象,地图不显示
```java
mNavi = AMapNavi.getInstance(MyApplication.getINSTANCE());
```
明明是初始化过的，却告诉我没对象！太可恶了。  
下面是高德给的说明：
> 请注意：Android 64位操作系统设备可以采用兼容模式运行32位的库文件，需要您在Eclipse、Android Studio配置工程的时候，在libs下面只保留一个armeabi文件夹，其余可以去除。  

受到下面这句话的启发：
> Apk安装到手机时自动识别了cpu的架构，只安装了我手机v7a的so。

我还用到 JPush 的 SDK ，其他包又不能删，那就把 armeabi 文件夹下的导航相关的库文件拷到其他目录下咯。  
导航 + 3D 地图，成功显示。



## 参考链接
- [高德地图sdk的AMapNavi.getInstance为null解决办法](http://www.cnblogs.com/xdao/p/gaode_map.html)
- [aMapNavi=AMapNavi.getInstance(getApplicationContext()); 出错](http://lbsbbs.amap.com/forum.php?mod=viewthread&tid=10578&highlight=AMapNavi.getInstance)
