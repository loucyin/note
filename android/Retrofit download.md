## 思路
- Retrofit2.0 依赖于 OkHttp
- OkHttp 需要重写 ResponseBody ，添加拦截，实现进度监听
- 取消下载

## OkHttp 添加拦截器
功能拦截 Response ，对 Response 进行包装，添加 ProgressListener
```java
if(mClient == null){
    mClient = new OkHttpClient.Builder()
            .addNetworkInterceptor(new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                    Response originalResponse = chain.proceed(chain.request());
                    return originalResponse.newBuilder()
                            .body(new DownloadResponseBody(originalResponse.body(), mDownloadListener))
                            .build();
                }
            })
            .build();
}
```

## Retrofit 文件下载
```java
@GET
Observable<ResponseBody> download(@Url String url );
```
文件下载就是一个一个链接而已，

## 参考链接
- [解决Retrofit文件下载进度显示问题](http://blog.csdn.net/ljd2038/article/details/51189334)
- [OkHttp Samples](https://github.com/square/okhttp/blob/master/samples/guide/src/main/java/okhttp3/recipes/Progress.java)
- [Retrofit + Okhttp cancel operation not working](http://stackoverflow.com/questions/31474742/retrofit-okhttp-cancel-operation-not-working/31476943#31476943)
