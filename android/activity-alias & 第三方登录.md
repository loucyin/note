# activity-alias

## 微信登录

### App 签名

之前使用的高德地图、百度地图、极光推送，都是使用签名文件的 `SHA1` 码做签名。<br>
微信使用的是 `MD5` 做文件签名，而且是小写的不带 `:` 的。<br>
签名配置不正确，没有办法调起微信。

### 使用微信登录的一些规定

接收微信的请求及返回值

> - 在你的包名相应目录下新建一个 wxapi 目录，并在该 wxapi 目录下新增一个 WXEntryActivity 类，该类继承自 Activity
> - 实现 IWXAPIEventHandler 接口，微信发送的请求将回调到 onReq 方法，发送到微信请求的响应结果将回调到 onResp 方法

在调起微信登录后，结果会返回到 WXEntryActivity 中处理。如果不想新建目录，也不想添加 Activity 。这时候 activity-alias 就起作用了。

```xml
<activity android:screenOrientation="portrait" android:name=".self.LoginActivity" android:launchMode="singleTask"/>
<activity-alias android:name=".wxapi.WXEntryActivity"
                android:targetActivity=".self.LoginActivity"
          android:label="@string/app_name"
          android:exported="true"/>
```

**注意事项**<br>
activity-alias 下，android:targetActivity 中使用的 Activity 必须在 activity-alias 之前声明。

### 使用方法

- 注册到微信

  ```java
  private void regToWx(){
    if(mWxApi == null){
        mWxApi = WXAPIFactory.createWXAPI(this,WX_APP_ID,true);
        mWxApi.registerApp(WX_APP_ID);
    }
  }
  ```

- 发送请求到微信

  ```java
  private void requestWxAuth(){
    // 检测微信是否安装
    if(!mWxApi.isWXAppInstalled()){
        showMsg("您没有安装微信客户端，请安装后再试");
        return;
    }

    // 请求权限
    final SendAuth.Req req = new SendAuth.Req();
    req.scope = "snsapi_userinfo";
    req.state = "wechat_sdk_demo_test";
    if(mWxApi.sendReq(req)){
        onLoading();
    }else {
        showMsg("发送请求到微信失败");
    }
  }
  ```

- 获取 access_token<br>
  发送请求成功后，会调起微信授权登录界面，响应结果会反馈到 WXEntryActivity 中处理。

  ```java
  @Override
  protected void onNewIntent(Intent intent) {
    super.onNewIntent(intent);
    Log.i(TAG, "onNewIntent: ");
    handleIntent(intent);
  }
  private void handleIntent(Intent intent) {
    Log.i(TAG, "handleIntent: ");
    if(intent.getExtras()!= null) {
        SendAuth.Resp resp = new SendAuth.Resp(intent.getExtras());
        switch (resp.errCode) {
            case BaseResp.ErrCode.ERR_OK:
                mPresenter.getWxAccessTokenByCode(resp.code);
                break;
            case BaseResp.ErrCode.ERR_USER_CANCEL:
                onLoadSuccess();
                showMsg("用户取消授权");
                break;
            case BaseResp.ErrCode.ERR_AUTH_DENIED:
                onLoadSuccess();
                showMsg("认证失败");
                break;
            default:
                onLoadSuccess();
                break;
        }
    }
  }
  ```

  **关于 onNewIntent ()**

  > 如果IntentActivity处于任务栈的顶端，也就是说之前打开过的Activity，现在处于:<br>
  > onPause 或者 onStop 状态，其他应用再发送Intent的时候，执行顺序为：

  > - onNewIntent
  > - onRestart
  > - onStart
  > - onResume

- 通过 code 换取 access_token<br>
  在上一步可以获取到 code ，通过 code 请求：

  ```
  https://api.weixin.qq.com/sns/oauth2/access_token?appid=APPID&secret=SECRET&code=CODE&grant_type=authorization_code
  ```

  **参数说明**

  - appid secret 在微信应用审核通过后获得
  - code : 上一步获取到的 code
  - grant_type : authorization_code

  **返回结果**<br>

  - 正确的：

    ```json
    {
    "access_token":"ACCESS_TOKEN",
    "expires_in":7200,
    "refresh_token":"REFRESH_TOKEN",
    "openid":"OPENID",
    "scope":"SCOPE",
    "unionid":"o6_bmasdasdsad6_2sgVt7hMZOPfL"
    }
    ```

  - 错误的：

    ```json
    {"errcode":40029,"errmsg":"invalid code"}
    ```

- 通过 openid 判断用户是否已经绑定本地用户

  - 已经绑定，自动登录
  - 未绑定本地用户，获取用户信息，注册

- 获取用户信息 接口地址：

  ```
  https://api.weixin.qq.com/sns/userinfo?access_token=ACCESS_TOKEN&openid=OPENID
  ```

  返回结果：

  ```json
  {
  "openid":"OPENID",
  "nickname":"NICKNAME",
  "sex":1,
  "province":"PROVINCE",
  "city":"CITY",
  "country":"COUNTRY",
  "headimgurl": "http://wx.qlogo.cn/mmopen/g3MonUZtNHkdmzicIlibx6iaFqAc56vxLSUfpb6n5WKSYVY0ChQKkiaJSgQ1dZuTOgvLLrhJbERQQ4eMsv84eavHiaiceqxibJxCfHe/0",
  "privilege":[
  "PRIVILEGE1",
  "PRIVILEGE2"
  ],
  "unionid": " o6_bmasdasdsad6_2sgVt7hMZOPfL"
  }
  ```

--------------------------------------------------------------------------------

## QQ 登录

QQ SDK 封装比微信登录封装做的好一些。

```java
public void getQQAuth() {
  if (mTencent == null) {
      mTencent = Tencent.createInstance(Config.QQ_APP_ID, MyApplication.getINSTANCE());
  }
  mTencent.login((Activity) mView, QQ_SCOPE, getQQAuthListener());
}
public IUiListener getQQAuthListener() {
  if (mQQAuthListener == null) {
      mQQAuthListener = new IUiListener() {
          @Override
          public void onComplete(Object o) {
              if (o instanceof JSONObject) {
                  initOpenidAndToken((JSONObject) o);
                  // 使用 qqOpenId 登录
                  String openId = mTencent.getOpenId();
                  authLogin(openId, LoginView.TYPE_QQ);
              }
          }

          @Override
          public void onError(UiError uiError) {
              mView.onLoadSuccess();
              mView.showMsg("获取授权失败");
          }

          @Override
          public void onCancel() {
              mView.onLoadSuccess();
              mView.showMsg("用户取消授权");
          }
      };
  }
  return mQQAuthListener;
}
```

## 参考链接

- [Android onNewIntent() 的作用](http://zhidao.baidu.com/link?url=BtE9mQgwXrAExEB-9DoJScyGXeZskF0WcGRQ2tnJmw8EHueidUztYkXxodmj88efFDwCYpzlnO0V9lm0jParq9nVwYy_8PLmVfmfoGOLYYm)
- [onNewIntent 调用时机](http://www.cnblogs.com/zenfly/archive/2012/02/10/2345196.html)
- [QQ API 调用说明](http://wiki.open.qq.com/wiki/mobile/API%E8%B0%83%E7%94%A8%E8%AF%B4%E6%98%8E#1.1_.E7.99.BB.E5.BD.95.2F.E6.A0.A1.E9.AA.8C.E7.99.BB.E5.BD.95.E6.80.81)
- [微信 Android 接入指南](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=1417751808&token=36e27d9445e22405d1383abf227203294eb558e6&lang=zh_CN)
- [移动应用微信登录开发指南](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419317851&token=36e27d9445e22405d1383abf227203294eb558e6&lang=zh_CN)
- [授权后接口调用](https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419317853&lang=zh_CN)
