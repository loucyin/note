## js 填充表单数据
```javascript
function fillData() {
  document.getElementById('version').value=12;
  document.getElementsByName('versionName')[0].value="1.0.12";
}
```
## WebView 执行 js
当 WebView 加载完之后，通过 webview.loadUrl("javascript:{}") 注入 JS。
```java
mWebView.setWebViewClient(new WebViewClient() {
    @Override
    public boolean shouldOverrideUrlLoading(WebView view, String url) {
        view.loadUrl(url);
        return true;
    }

    @Override
    public void onPageFinished(WebView view, String url) {
        String js = createFillFormJs();
        if(js != null) {
            view.loadUrl(js);
        }
        // 登录成功后加载框取消
        super.onPageFinished(view, url);
        Log.i(TAG, "Finished: ");
    }
});
```
## 封装自动填充表单的方法
```java
private String createFillFormJs(){
    if(mNamespaces == null || mValues == null){
        return null;
    }

    if(mIndexes == null && mValues.length != mNamespaces.length){
        return null;
    }

    if(mIndexes != null ){
        if(mValues.length != mNamespaces.length || mValues.length != mIndexes.length){
            return null;
        }
    }

    String js = "javascript: {";
    for (int i = 0; i < mNamespaces.length; i++) {
        String name = mNamespaces[i];
        String value = mValues[i];
        int index = 0;
        if(mIndexes != null){
            index = mIndexes[i];
        }
        String fill = "document.getElementsByName('"+name+"')["+index+"].value = '"+value+"';";
        js += fill;
    }
    js += "a=11;}";
    return js;
}
```
小尾巴 `a=11;` 不加个小尾巴，加载完 JS 后，会是另外一种风格。 

## 参考链接
- [android webView网页表单自动登录（单点登录）](http://www.android100.org/html/201503/19/128692.html)
- [webview javascript 注入方法](http://www.cnblogs.com/rayray/p/3680500.html)
- [javascript中getElementsByName和getElementById的区别](http://blog.csdn.net/bzuld/article/details/6619730)
