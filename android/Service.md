## 获取传入 Service 中的数据
通过 startService 启动 Service 时，通过 Intent 传入 Service 中的数据，可以使用下面的方式获取。
```java
@Override
public int onStartCommand(Intent intent, int flags, int startId) {
    Bundle bundle = intent.getExtras();
    mDownloadVersion = bundle.getInt("version");
    update();
    return super.onStartCommand(intent, flags, startId);
}
```
## startService
只会生成一个 Service 实例。Service 的 onCreate 只会调用一次， 通过 startService 启动 Service 一次， onStartCommand 就会调用一次。
