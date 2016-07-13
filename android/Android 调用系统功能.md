## 调用拨号
```java
intent = new Intent(Intent.ACTION_DIAL, Uri.parse(TELEPHONE));
intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
startActivity(intent);
```
## 安装 apk
- 更改权限  
```java
String command     = "chmod " + permission + " " + path;
Runtime runtime = Runtime.getRuntime();
runtime.exec(command);
```
- 使用 Intent 调用安装
```java
Intent intent = new Intent(Intent.ACTION_VIEW);
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
intent.setDataAndType(Uri.parse("file://" + path),"application/vnd.android.package-archive");
context.startActivity(intent);
```
## 参考链接
