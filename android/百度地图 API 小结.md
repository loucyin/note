## 姿势不对，导致的坑
### 使用百度地图定位，只能定位一次
>在application标签中声明service组件,每个app拥有自己单独的定位service
```xml
<service android:name="com.baidu.location.f" android:enabled="true" android:process=":remote">
</service>
```

### 
