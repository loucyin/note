# Retrofit download file

## 思路

- Retrofit2.0 依赖于 OkHttp
- OkHttp 需要重写 ResponseBody ，添加拦截，实现进度监听
- 实现，取消下载
- UI显示

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

- GET 请求

  ```java
  @GET
  Observable<ResponseBody> download(@Url String url );
  ```

  文件下载就是一个链接而已，文件下载完成后，会返回 ResponseBody ，这种方法会把下载的内容放到内存里，下载文件比较大时，会出现 oom 。

- @Streaming

  ```java
  @Streaming
  @GET
  Observable<ResponseBody> download(@Url String url );
  ```

  > With @Streaming we can get raw input From ResponseBody.

  ```java
  BufferedSource source = responseBody.source();
  while (!source.exhausted()) {
  }
  ```

  使用 @Streaming 后，需要自己处理数据流，可以通过 BufferedSource.exhausted() 方法判断是否还有数据。

## OkIo 操作文件

```java
File file = new File(savePath);
BufferedSource source = responseBody.source();
BufferedSink bufferedSink = = Okio.buffer(Okio.buffer(file));
Buffer buffer = new Buffer();
long count = source.read(buffer, 1024);
bufferedSink.write(buffer, count);
```

OkIo 中通过 Source 处理输入流，通过 Sink 处理输出流。

## 断点续传

> 所谓断点续传，也就是要从文件已经下载的地方开始继续下载。在以前版本的 HTTP 协议是不支持断点的，HTTP/1.1 开始就支持了。一般断点下载时才用到 Range 和 Content-Range 实体头。

在请求下载时，加入请求头：

```
Range: bytes=0-801
```

修改请求接口，加入 Range 信息。

```java
@Streaming
@GET
Observable<ResponseBody> download(@Header("Range") String start,@Url String url);
```

## 取消下载

OkHttp 3 中的 OkHttpClient 没有 cancel 方法了，尝试使用 OkHttpClient 取消下载，没有成功。<br>
在 stackoverflow 找到一个解决方法，通过实现 Callback 接口，自定义 CancelableCallback ,实现取消请求。<br>
我使用的是 RxJava ，直接保存 Subscription ,使用 Subscription.unsubscribe() 方法取消订阅，就可以实现请求的取消。取消订阅后会产生 InterruptedIOException ，异常处理中捕获 InterruptedIOException 捕捉取消下载操作。

## 监听接口

```java
public interface DownloadListener {
    void onStart();
    void onStop();
    void onProgress(long progress, long total);
    void onSuccess(File file);
    void onError(Throwable throwable);
}
```

## 下载管理

需要实现的功能：

- 启动下载
- 停止下载
- 实现文件缓存

## 参考链接

- [解决Retrofit文件下载进度显示问题](http://blog.csdn.net/ljd2038/article/details/51189334)
- [OkHttp Samples](https://github.com/square/okhttp/blob/master/samples/guide/src/main/java/okhttp3/recipes/Progress.java)
- [Retrofit + Okhttp cancel operation not working](http://stackoverflow.com/questions/31474742/retrofit-okhttp-cancel-operation-not-working/31476943#31476943)
- [Android Retrofit 2 + RxJava: listen to endless stream](http://stackoverflow.com/questions/36603368/android-retrofit-2-rxjava-listen-to-endless-stream)
- [HTTP 断点续传协议头部分析](http://blog.csdn.net/wwj_748/article/details/19935699)
