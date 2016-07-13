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

## 高德导航的大深坑
```
06-14 08:33:25.940 30686-30686/  W/ResourceType: No package identifier when getting value for resource number 0x00000000
06-14 08:33:25.950 30686-30686/  W/ResourceType: No package identifier when getting value for resource number 0x00000000
06-14 08:33:25.952 30686-30686/  W/ResourceType: No package identifier when getting value for resource number 0x00000000
06-14 08:33:25.953 30686-30686/  W/ResourceType: No package identifier when getting value for resource number 0x00000000
06-14 08:33:25.995 30686-30686/  W/ResourceType: For resource 0x7f020089, entry index(137) is beyond type entryCount(115)
06-14 08:33:25.995 30686-30686/  W/ResourceType: Failure getting entry for 0x7f020089 (t=1 e=137) (error -75)
06-14 08:33:25.995 30686-30686/  W/System.err: android.view.InflateException: Binary XML file line #249: Error inflating class ImageView
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:763)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.rInflate(LayoutInflater.java:806)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.rInflate(LayoutInflater.java:809)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.rInflate(LayoutInflater.java:809)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.inflate(LayoutInflater.java:504)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.inflate(LayoutInflater.java:385)
06-14 08:33:25.996 30686-30686/  W/System.err:     at com.autonavi.tbt.g.a(ResourcesUtil.java:245)
06-14 08:33:25.996 30686-30686/  W/System.err:     at com.amap.api.navi.AMapNaviViewCore.init(AMapNaviViewCore.java:361)
06-14 08:33:25.996 30686-30686/  W/System.err:     at com.amap.api.navi.AMapNaviView.init(AMapNaviView.java:187)
06-14 08:33:25.996 30686-30686/  W/System.err:     at com.amap.api.navi.AMapNaviView.<init>(AMapNaviView.java:41)
06-14 08:33:25.996 30686-30686/  W/System.err:     at java.lang.reflect.Constructor.newInstance(Native Method)
06-14 08:33:25.996 30686-30686/  W/System.err:     at java.lang.reflect.Constructor.newInstance(Constructor.java:288)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.createView(LayoutInflater.java:607)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:743)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.rInflate(LayoutInflater.java:806)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.inflate(LayoutInflater.java:504)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.inflate(LayoutInflater.java:414)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.view.LayoutInflater.inflate(LayoutInflater.java:365)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.support.v7.app.AppCompatDelegateImplV7.setContentView(AppCompatDelegateImplV7.java:280)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.support.v7.app.AppCompatActivity.setContentView(AppCompatActivity.java:140)
06-14 08:33:25.996 30686-30686/  W/System.err:     at java.lang.reflect.Method.invoke(Native Method)
06-14 08:33:25.996 30686-30686/  W/System.err:     at java.lang.reflect.Method.invoke(Method.java:372)
06-14 08:33:25.996 30686-30686/  W/System.err:     at org.xutils.view.ViewInjectorImpl.inject(ViewInjectorImpl.java:82)
06-14 08:33:25.996 30686-30686/  W/System.err:     at  .map.NaviViewActivity.onCreate(NaviViewActivity.java:30)
06-14 08:33:25.996 30686-30686/  W/System.err:     at  .navigation.NavigationActivity.onCreate(NavigationActivity.java:19)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.app.Activity.performCreate(Activity.java:5993)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.app.Instrumentation.callActivityOnCreate(Instrumentation.java:1111)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:2432)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:2541)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.app.ActivityThread.access$1000(ActivityThread.java:168)
06-14 08:33:25.996 30686-30686/  W/System.err:     at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1436)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.os.Handler.dispatchMessage(Handler.java:111)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.os.Looper.loop(Looper.java:194)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.app.ActivityThread.main(ActivityThread.java:5628)
06-14 08:33:25.997 30686-30686/  W/System.err:     at java.lang.reflect.Method.invoke(Native Method)
06-14 08:33:25.997 30686-30686/  W/System.err:     at java.lang.reflect.Method.invoke(Method.java:372)
06-14 08:33:25.997 30686-30686/  W/System.err:     at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:956)
06-14 08:33:25.997 30686-30686/  W/System.err:     at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:751)
06-14 08:33:25.997 30686-30686/  W/System.err: Caused by: android.content.res.Resources$NotFoundException: Resource ID #0x7f020089
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.content.res.Resources.getValue(Resources.java:1498)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v7.widget.ResourcesWrapper.getValue(ResourcesWrapper.java:204)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v7.widget.AppCompatDrawableManager.loadDrawableFromDelegates(AppCompatDrawableManager.java:321)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v7.widget.AppCompatDrawableManager.getDrawable(AppCompatDrawableManager.java:197)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v7.widget.TintTypedArray.getDrawableIfKnown(TintTypedArray.java:70)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v7.widget.AppCompatImageHelper.loadFromAttributes(AppCompatImageHelper.java:42)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v7.widget.AppCompatImageView.<init>(AppCompatImageView.java:65)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v7.widget.AppCompatImageView.<init>(AppCompatImageView.java:53)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v7.app.AppCompatViewInflater.createView(AppCompatViewInflater.java:106)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v7.app.AppCompatDelegateImplV7.createView(AppCompatDelegateImplV7.java:980)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v7.app.AppCompatDelegateImplV7.onCreateView(AppCompatDelegateImplV7.java:1039)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.support.v4.view.LayoutInflaterCompatHC$FactoryWrapperHC.onCreateView(LayoutInflaterCompatHC.java:44)
06-14 08:33:25.997 30686-30686/  W/System.err:     at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:725)
```
神奇的错误，而且找不到出处。最后发现用不能用 AppCompatActivity 来装 AMapNaviView  竟然是 support v7 包的问题！将 AppCompatActivity 换成 Activity 问题消失。

## 参考链接
- [高德地图sdk的AMapNavi.getInstance为null解决办法](http://www.cnblogs.com/xdao/p/gaode_map.html)
- [aMapNavi=AMapNavi.getInstance(getApplicationContext()); 出错](http://lbsbbs.amap.com/forum.php?mod=viewthread&tid=10578&highlight=AMapNavi.getInstance)
