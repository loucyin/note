# android 6.0 权限问题

## 临时解决方案

```
targetSdkVersion 22
```

设置 targetSdkVersion 小于 23，会默认给予所有授权。

## 权限处理方式

### 高德地图

- 一共涉及到 7 个敏感权限<br>
  位置信息、存储空间、电话、相机、短信、通讯录、麦克风。<br>

- 处理方式<br>
  高德会在启动界面检测权限，位置信息、存储空间、电话是必须的权限，要是不给，直接退出没法使用。<br>
  其他几项权限在使用到的时候检测。

### UC 浏览器

处理方式也与高德类似。<br>

- 一共涉及到 6 个敏感权限<br>
  位置信息、存储空间、电话、相机、短信、麦克风。<br>

- 处理方式<br>
  UC 会在启动界面检测权限，存储空间、电话是必须的权限，要是不给，直接退出没法使用。<br>
  其他几项权限在使用到的时候检测。

### 处理方式

- 应用程序必须的权限<br>
  启动页检测授权，取消授权的，引导用户到设置界面去授权，对用户坚决拒绝授权的，直接退出 APP 。
- 功能性需求权限<br>
  在用户使用的时候检测处理。

## Android 与权限处理相关的 API

```java
public int checkSelfPermission(String permission);
public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults);
public final void requestPermissions(@NonNull String[] permissions, int requestCode);
```

- checkSelfPermission 是 Context 下面的一个方法，用于检测是否获取到授权。
- onRequestPermissionsResult 是 FragmentActivity 下的一个方法，用于处理授权结果。
- requestPermissions 是 Activity 下面的一个方法，用于获取授权。

## 权限的处理

单个权限的检测

```java
private void checkPermission() {
    int hasPermission = 0;
    if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.M) {
        // 获取授权状态
        hasPermission = checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE);
        // 授权被取消，并不再显示的情况下
        if(hasPermission == PackageManager.PERMISSION_DENIED){
            // 显示提示信息，并指导用户进行授权
            showRequestPermissionDialog();
        }
        // 未获取授权的情况下
        else if (hasPermission != PackageManager.PERMISSION_GRANTED) {
            requestPermissions(new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE},
                    REQUEST_CODE_ASK_PERMISSIONS);
            return;
        }
    }
}
```

授权结果的处理

```java
@Override
public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    if (requestCode == REQUEST_CODE_ASK_PERMISSIONS) {
        for (int i = 0; i < grantResults.length; i++) {
            int result = grantResults[i];
            if(result == PackageManager.PERMISSION_DENIED){
                // 打印出没有获取到授权的权限信息
                Log.i(TAG, "Get permission :"+permissions[i]+" denied ");
            }
        }
    }
}
```

跳转到应用详情页（用于未获取到授权的情况下，指引用户设置授权）

```java
private void toSettingPermission(){
    Intent intent = new Intent();
    intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
    intent.setData(Uri.fromParts("package", getPackageName(), null));
    startActivity(intent);
}
```

## PermissionsDispatcher

```java
@RuntimePermissions
public class MainActivity extends AppCompatActivity {
  private static final String TAG = "MainActivity";

  @Override
  protected void onCreate(@Nullable Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_main);
      MainActivityPermissionsDispatcher.needExternalStorageWithCheck(this);
  }

  @NeedsPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE)
  void needExternalStorage(){
      Log.i(TAG, "needExternalStorage: ");
  }

  @OnShowRationale(Manifest.permission.WRITE_EXTERNAL_STORAGE)
  void onShowExternalStorageRational(final PermissionRequest request){
      // 可以添加对话框，显示为何需要权限
      request.proceed();
  }

  @OnPermissionDenied(Manifest.permission.WRITE_EXTERNAL_STORAGE)
  void onExternalStoragePermissionDenied(){
      Log.i(TAG, "onPermissionDenied: ");
  }

  @OnNeverAskAgain(Manifest.permission.WRITE_EXTERNAL_STORAGE)
  void onExternalStorageNeverAsk(){
      // 显示对话框，引导用户启用权限
      Log.i(TAG, "onExternalStorageNeverAsk: ");
  }

  @Override
  public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
      super.onRequestPermissionsResult(requestCode, permissions, grantResults);
      MainActivityPermissionsDispatcher.onRequestPermissionsResult(this,requestCode,grantResults);
  }
}
```

框架是个好东西，少了一堆的 if else 。

- 编译后会生成 MainActivityPermissionsDispatcher 类，使用：

  ```java
  MainActivityPermissionsDispatcher.needExternalStorageWithCheck(this);
  ```

  方法检测权限。

- 在 MainActivity 的 onRequestPermissionsResult 方法中使用：

  ```java
  MainActivityPermissionsDispatcher.onRequestPermissionsResult(this,requestCode,grantResults);
  ```

  处理权限请求结果。

  PermissionsDispatcher 注解的解释：

Annotation            | Required | Description
--------------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
`@RuntimePermissions` | **✓**    | Register an `Activity` or `Fragment` to handle permissions
`@NeedsPermission`    | **✓**    | Annotate a method which performs the action that requires one or more permissions
`@OnShowRationale`    |          | Annotate a method which explains why the permission/s is/are needed. It passes in a `PermissionRequest` object which can be used to continue or abort the current permission request upon user input
`@OnPermissionDenied` |          | Annotate a method which is invoked if the user doesn't grant the permissions
`@OnNeverAskAgain`    |          | Annotate a method which is invoked if the user chose to have the device "never ask again" about a permission

## 参考链接

- [Android 6.0 运行时权限处理完全解析](http://www.w2bc.com/article/101798?from=extend)
- [Android M(6.0) 权限爬坑之旅](http://www.open-open.com/lib/view/open1445671646351.html)
- [System Permissions](https://developer.android.com/guide/topics/security/permissions.html#defining)
- [谈谈Android 6.0运行时权限理解](http://www.cnblogs.com/cr330326/p/5181283.html)
- [聊一聊 Android 6.0 的运行时权限](http://www.360doc.com/content/16/0118/17/29640067_528889420.shtml)
- [android 6.0权限全面详细分析和解决方案](http://blog.csdn.net/hudashi/article/details/50775180)
- [PermissionsDispatcher](https://github.com/hotchemi/PermissionsDispatcher)
- [RxPermissions](https://github.com/tbruyelle/RxPermissions)
