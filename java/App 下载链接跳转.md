# APP 下载链接跳转

## 思路

- 一个二维码实现 Android、IOS APP 下载，需要区分客户端，根据不同的客户端跳转不同的链接。
- 在 HttpRequest Header 中有一个 UserAgent 的参数，记录着浏览器的信息。

  - IPhone 微信

    ```
    Mozilla/5.0 (iPhone; CPU iPhone OS 10_0_2 like Mac OS X) AppleWebKit/602.1.50 (KHTML, like Gecko) Mobile/14A456 MicroMessenger/6.3.25 NetType/WIFI Language/zh_CN
    ```

  - IPhone QQ

    ```
    Mozilla/5.0 (iPhone; CPU iPhone OS 10_0_2 like Mac OS X) AppleWebKit/602.1.50 (KHTML, like Gecko) Mobile/14A456 QQ/6.5.7.408 V1_IPH_SQ_6.5.7_1_APP_A Pixel/640 Core/UIWebView NetType/WIFI Mem/299
    ```

  - IPhone Safari

    ```
    Mozilla/5.0 (iPhone; CPU iPhone OS 10_0_2 like Mac OS X) AppleWebKit/602.1.50 (KHTML, like Gecko) Version/10.0 Mobile/14A456 Safari/602.1
    ```

  - Mac

    ```
    Mozilla/5.0 (Macintosh; Intel Mac OS X 10_11_5) AppleWebKit/601.6.17 (KHTML, like Gecko) Version/9.1.1 Safari/601.6.17
    ```

  - Android QQ

    ```
    Mozilla/5.0 (Linux; Android 6.0.1; MI 5 Build/MXB48T) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/37.0.0.0 Mobile MQQBrowser/6.8 TBS/036872 Safari/537.36 V1_AND_SQ_6.5.8_422_YYB_D PA QQ/6.5.8.2910 NetType/WIFI WebP/0.3.0 Pixel/1080
    ```

  - Android 微信

    ```
    Mozilla/5.0 (Linux; Android 6.0.1; MI 5 Build/MXB48T) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/37.0.0.0 Mobile MQQBrowser/6.8 TBS/036872 Safari/537.36 MicroMessenger/6.3.27.880 NetType/WIFI Language/zh_CN
    ```

  - Android QQBrowser

    ```
    Mozilla/5.0 (Linux; U; Android 6.0.1; zh-cn; MI 5 Build/MXB48T) AppleWebKit/537.36 (KHTML, like Gecko)Version/4.0 Chrome/37.0.0.0 MQQBrowser/7.0 Mobile Safari/537.36
    ```

  - Android UC

    ```
    Mozilla/5.0 (Linux; U; Android 6.0.1; zh-CN; MI 5 Build/MXB48T) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 UCBrowser/11.1.5.871 U3/0.8.0 Mobile Safari/534.30
    ```

- 客户端系统分类：

系统      | UserAgent 中包含的字符
:------ | :---------------
Android | Android
IPhone  | iPhone
Other   |

- Android 应用

应用     | UserAgent 中包含的字符
:----- | :---------------------------------------------
QQ     | `Android` `Mobile MQQBrowser`
微信     | `Android` `Mobile MQQBrowser` `MicroMessenger`
QQ 浏览器 | `Android` `MQQBrowser`

- IPhone 应用

应用 | UserAgent 中包含的字符
:- | :------------------------
微信 | `iPhone` `MicroMessenger`

- 几种情况

  1. Android 可以直接通过链接下载 APP。
  2. Android 微信扫码不能直接下载 APP，解决方法：一是提示用户在浏览器中打开链接；二是跳转到应用宝推广链接。
  3. IPhone 只能通过，跳转 AppleStore 进行下载。
  4. IPhone 微信中，不能跳转 AppleStore，需要提示用户在浏览器中打开链接。

## JS 处理

```html
<script type="text/javascript">
    /**
     * Android ：
     * 1、微信中扫码不能直接打开下载链接，可以跳转到应用宝下载；
     * 2、其他浏览器中可以直接跳转到下载链接。
     * iPhone：
     * 1、微信中不能跳转到  AppleStore,需要添加提示
     * other:
     * 直接显示提示下载页面。
     */
    var typeAndroidQQ = 1;
    var typeAndroidWX = 2;
    var typeAndroidOther = 3;

    var typeIphoneWx = 4;
    var typeIphoneOther = 5;

    var typeBrowserOther = 6;

    var qqAppStore = "http://a.app.qq.com/o/simple.jsp?pkgname=com.shuichengchuxing.app";
    var appleStore = "https://itunes.apple.com/cn/app/id1169377504";
    var androidAppDownload = "getLatestApp";

    function getBrowserType() {
        var ua = navigator.userAgent;
        var android = "Android";
        var mobileQQ = "Mobile MQQBrowser";
        var iPhone = "iPhone";
        var weixin = "MicroMessenger";

        if (ua.match(/Android/i) == android) {
            if (ua.match(/Mobile MQQBrowser/i) == mobileQQ) {
                if (ua.match(/MicroMessenger/i)) {
                    return typeAndroidWX;
                } else {
                    return typeAndroidQQ;
                }
            } else {
                return typeAndroidOther;
            }
        } else if (ua.match(/iPhone/i)) {
            if (ua.match(/MicroMessenger/i)) {
                return typeIphoneWx;
            } else {
                return typeIphoneOther;
            }
        } else {
            return typeBrowserOther;
        }

    }
    function loadHtml() {
        var div = document.createElement('div');
        div.id = 'weixin-tip';
        div.innerHTML = '<p><img src="img/live_weixin.png" alt="微信打开"/></p>';
        document.body.appendChild(div);
    }

    function loadStyleText(cssText) {
        var style = document.createElement('style');
        style.rel = 'stylesheet';
        style.type = 'text/css';
        try {
            style.appendChild(document.createTextNode(cssText));
        } catch (e) {
            style.styleSheet.cssText = cssText; //ie9以下
        }
        var head = document.getElementsByTagName("head")[0]; //head标签之间加上style样式
        head.appendChild(style);
    }

    switch (getBrowserType()) {
  // Android qq 微信跳转应用宝
    case typeAndroidQQ:
    case typeAndroidWX:
        location.href = qqAppStore;
        break;
  // Android 其他情况直接下载
    case typeAndroidOther:
        location.href = androidAppDownload;
        break;
  // iPhone 微信提示在浏览器中打开链接
    case typeIphoneWx:
        var winHeight = typeof window.innerHeight != 'undefined' ? window.innerHeight
                : document.documentElement.clientHeight;
        var cssText = "#weixin-tip{position: fixed; left:0; top:0; background: rgba(0,0,0,0.8); filter:alpha(opacity=80); width: 100%; height:100%; z-index: 100;} #weixin-tip p{text-align: center; margin-top: 10%; padding:0 5%;}";
        loadHtml();
        loadStyleText(cssText);
        break;
  // iPhone 其他情况打开 appleStore 链接
    case typeIphoneOther:
        location.href = appleStore;
        break;
    }
</script>
```

## 参考链接

- [微信打开网址添加在浏览器中打开提示](http://caibaojian.com/weixin-tip.html)
- [kujian/weixinTip](https://github.com/kujian/weixinTip)
