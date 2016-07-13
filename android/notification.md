## 通知栏显示通知
- 初始化 notification  
```java
PendingIntent intent = PendingIntent.getActivity(mView,1,new Intent(Intent.ACTION_MAIN),PendingIntent.FLAG_ONE_SHOT);
Notification.Builder builder = new  Notification.Builder(mView)
        .setContentTitle("test")
        .setContentText("hello")
        .setSmallIcon(R.mipmap.ic_launcher)
        .setContentIntent(intent);
if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.JELLY_BEAN) {
    notification = builder.build();
}else {
    notification = builder.getNotification();
}
```
- 显示 notification  
```java
NotificationManager manager = (NotificationManager) mView.getSystemService(Context.NOTIFICATION_SERVICE);
manager.notify(1,notification);
```
神奇的事情，不设置 Icon Notification 不显示。

## 自定义 Notification 视图
- Notification 中加入自定义视图
```java
RemoteViews view = new RemoteViews(getPackageName(), R.layout.download);
builder.setContent(view);
```
- 通过 RemoteViews 操作自定义视图
```java
mViews.setTextViewText(R.id.tvProgress, "" + progress);
mViews.setProgressBar(R.id.barLoading, 100, progress, false);
```
更改后需要，重新 notify ，来刷新显示。

## Notification 中的事件处理
思路：通过 broadcast 的方式，通知 activity 或者 service ，在 BroadcastReceiver 中处理事件。
Notification 的事件响应是通过 PendingIntent 实现的。
```java
private void initNotification() {
    // 启动暂停监听
    RemoteViews view = new RemoteViews(getPackageName(), R.layout.download);
    Intent buttonIntent = new Intent(STATUS_BAR_COVER_CLICK_ACTION);
    PendingIntent pendButtonIntent = PendingIntent.getBroadcast(MyUpdateService.this, 0, buttonIntent, 0);
    view.setOnClickPendingIntent(R.id.ivStart, pendButtonIntent);

    // 删除 Notification 监听
    Intent deleteNotification = new Intent(STATUS_BAR_DELETE);
    PendingIntent deleteIntent = PendingIntent.getBroadcast(MyUpdateService.this, 0, deleteNotification, 0);

    // 初始化 Notification
    mManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
    mNotification = null;
    Notification.Builder builder = new Notification.Builder(this)
            .setContent(view)
            .setDeleteIntent(deleteIntent);
    if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.JELLY_BEAN) {
        mNotification = builder.build();
    } else {
        mNotification = builder.getNotification();
    }
    mManager.notify(ID, mNotification);
}
private void initReceiver(){
  mReceiver = new BroadcastReceiver() {
      @Override
      public void onReceive(Context context, Intent intent) {
          if (intent.getAction().equals(STATUS_BAR_COVER_CLICK_ACTION)) {
              //在这里处理点击事件
              int status = mDownloadManager.getInfo().getState();
              switch (status) {
                  case DownloadInfo.WAITING:
                  case DownloadInfo.START:
                      mDownloadManager.stopDownload();
                      break;
                  case DownloadInfo.STOP:
                  case DownloadInfo.ERROR:
                      mDownloadManager.startDownload();
                      break;
              }
          } else if (intent.getAction().equals(STATUS_BAR_DELETE)) {
              Log.i("delete", "onReceive: ");
              mNotificationDeleted = true;
              mDownloadManager.stopDownload();
              stopSelf();
          }
      }
  };
  IntentFilter filter = new IntentFilter();
  filter.addAction(STATUS_BAR_COVER_CLICK_ACTION);
  filter.addAction(STATUS_BAR_DELETE);
  registerReceiver(mReceiver, filter);
}
```

## 参考链接
