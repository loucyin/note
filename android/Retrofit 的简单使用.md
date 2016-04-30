## 初次使用 Retrofit
### 获取 html
Retrofit 会把获取的数据自动解析为对象，但是我只需要获取 String 和 Image ，可以利用 ResponseBody 对象实现。
通过 Response 的 body() 方法获取 ResponseBody 对象，在通过 ResponseBody 的 string() 方法解析到 String 对象，
或者通过 ResponseBody 的 byteStream() 解析到一个 InputStream 对象。
- 首先，是 Service 接口
```java
public interface LocalHostService
{
  @GET("/")
  Call<ResponseBody> getHtml();

  @GET("{image}")
  Call<ResponseBody> getImage(@Path("image") String image);
}
```
- 请求 String 数据  
定义了一个 Listener 接口，用于请求成功之后的回调。Retrofit 的 Callback 会自动在 UI 线程运行，很神奇。
```java
public static final String BASE_URL = "http://192.168.1.150:8080/";
public interface OnRequestHtmlListener
{
  void onSuccess(String result);

  void onFailed(String msg);
}
public void requestHtml()
{
  Retrofit retrofit = new Retrofit.Builder().baseUrl(BASE_URL).build();

  LocalHostService service = retrofit.create(LocalHostService.class);

  Call<ResponseBody> call = service.getHtml();

  call.enqueue(new Callback<ResponseBody>()
  {
    @Override
    public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response)
    {
      if (mHtmlListener != null)
      {
        try
        {
          mHtmlListener.onSuccess(response.body().string());
        } catch (IOException e)
        {
          e.printStackTrace();
        }
      }
    }

    @Override
    public void onFailure(Call<ResponseBody> call, Throwable t)
    {
      t.printStackTrace();
      if (mHtmlListener != null)
      {
        mHtmlListener.onSuccess(t.getMessage());
      }
    }
  });
}
```
- 请求图片  
```java
public void requestImage(String image)
  {
    Retrofit retrofit = new Retrofit.Builder().baseUrl(BASE_URL).build();
    LocalHostService service = retrofit.create(LocalHostService.class);
    Call<ResponseBody> call = service.getImage(image);
    call.enqueue(new Callback<ResponseBody>()
    {
      @Override
      public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response)
      {
        Bitmap bitmap = BitmapFactory.decodeStream(response.body().byteStream());
        if (mImageListener != null)
        {
          mImageListener.onSuccess(bitmap);
        }
      }

      @Override
      public void onFailure(Call<ResponseBody> call, Throwable t)
      {
        t.printStackTrace();
        if (mImageListener != null)
        {
          mImageListener.onFailed(t.getMessage());
        }
      }
    });
  }
```
- 显示  
通过 TextView 显示刚才加载到的数据
```java
Spanned spanned = Html.fromHtml(html,new MyImageGetter(),null);
mTvShowHtml.setText(spanned);
```
需要实现一个 ImageGetter 接口解析图片。MyImageGetter 的实现思路是，MyImageGetter 中有一个 Drawable 对象，
在构造函数中初始化，在 getDrawable() 的时候，直接返回这个对象。请求到图片后在刷新 Drawable 对象。因为 BitmapDrawable
没有开放 setBitmap() 方法，所以还要写一个类继承 BitmapDrawable ，实现 setBitmap() 方法，以及 draw() 方法。
```java
@Override
public Drawable getDrawable(String s)
{
    mModel.requestImage(s);
    return mDrawable;
}
static class MyDrawable extends BitmapDrawable
{
    Bitmap mBitmap;

    public MyDrawable()
    {
      super();
    }

    @Override
    public void draw(Canvas canvas)
    {
      super.draw(canvas);
      if(mBitmap != null)
      {
        canvas.drawBitmap(mBitmap,0,0,getPaint());
      }
    }

    public void setBitmap(Bitmap bitmap)
    {
      mBitmap = bitmap;
      invalidateSelf();
    }
}
```
实现过程中，发现 AndroidStudio 不进行源码关联，系统是 Ubuntu 16.04 ，开发环境是 Android Studio 2.1 ,
Android Studio 提示下载完源码后，就没有下文了。
- 方案一  
修改 /.AndroidStudio/config/options/jdk.table.xml 对 <sourcePath> 节点插入如下代码：
```xml
<sourcePath>
    <root type="composite">
      <root type="simple" url="file:///home/user/Android/Sdk/sources/android-23" />
    </root>
 </sourcePath>
```
不知道是姿势不对还是什么的，没起作用，启动 AndroidStudio 后，改的东西就恢复回之前的样子。
- 方案二  
重新加载 Sdk，这个方法奏效了。在打开之前的文件看看：
```xml
<sourcePath>
    <root type="composite">
      <root type="simple" url="file://$USER_HOME$/Android/Sdk/sources/android-23" />
    </root>
</sourcePath>
```
神奇的事情，没发现有多大区别。
### 参考链接：
[Retrofit](http://square.github.io/retrofit/)  
[How to get response as String using retrofit without using GSON or any other library in android ](http://stackoverflow.com/questions/32617770/how-to-get-response-as-string-using-retrofit-without-using-gson-or-any-other-lib)  
[Retrofit API to retrieve a png image](http://stackoverflow.com/questions/25462523/retrofit-api-to-retrieve-a-png-image)  
[Android studio 关联源代码](http://blog.csdn.net/duguang77/article/details/46699453)  
[Android Studio: how to attach Android SDK sources?](http://stackoverflow.com/questions/21221679/android-studio-how-to-attach-android-sdk-sources)  
