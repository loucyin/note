## ImageGetter
在 TextView 中显示 html ，需要使用 ImageGetter 解析 <img> 标签。
ImageGetter 是一个接口，需要实现里面的方法。
```java
public Drawable getDrawable(String source);
```
## 简单的 ImageGetter 实现
创建一个 Drawable 对象，保留引用并返回，异步请求网络，获取图片，获取到图片后，转换为 Bitmap ，交给 Drawable 对象进行绘制。
- 自定义 Drawable 对象
```java
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
    }
}
```
- 实现 getDrawable() 方法
MyImageGetter 有一个 TextView 成员，用于保存用于图片显示的 TextView ，加载完成后，调用 TextView 的 invalidate() 方法刷新视图。
```java
public MyImageGetter(TextView view)
{
  mContainer = view;
}  
@Override
public Drawable getDrawable(String s)
{
  // 初始化占位 Drawable
  final MyDrawable drawable = new MyDrawable(mContainer.getResources(),null);

  // 初始化请求对象
  LocalHostModel model = new LocalHostModel();

  // 设置回调函数
  model.setImageListener(new LocalHostModel.OnRequestImageListener()
  {
    @Override
    public void onSuccess(Bitmap bitmap)
    {
      // 处理 bitmap 刷新视图
      drawable.setBitmap(bitmap);

      int height = bitmap.getHeight();
      int width = bitmap.getWidth();

      drawable.setBounds(0, 0, width, height);

      mContainer.invalidate();
    }

    @Override
    public void onFailed(String msg)
    {
      Log.i(TAG, "onFailed: " + msg);
    }
  });

  // 请求图片
  model.requestImage(s);

  return drawable;
}
```
## 图片的大小和显示位置  
- Drawable.setBounds(Rect) 方法
> The setBounds(Rect) method must be called to tell the Drawable where it is drawn and how large it should be. All Drawables should respect the requested size, often simply by scaling their imagery. A client can find the preferred size for some Drawables with the getIntrinsicHeight() and getIntrinsicWidth() methods.  

  Drawable.setBounds(Rect) 用于设置绘图的位置，和绘图的区域。

- Canvas.drawBitmap()
bitmap 要通过 Drawable.draw(Canvas) 函数绘制，可以通过 Canvas.drawBitmap() 控制 bitmap 在Drawable 中的显示位置。
- 控制图片居中显示  
1. 首先获取 TextView 的宽度，减去 paddingLeft paddingRight 即为 Drawable 可用的显示宽度；
2. 通过上面的两个方法配合，控制图片居中显示。
```java
private void handleBitmap(MyDrawable drawable, Bitmap srcBitmap)
{
  // 获取原 bitmap 的大小
  int srcHeight = srcBitmap.getHeight();
  int srcWidth = srcBitmap.getWidth();

  // drawable 可用的显示宽度
  int containerPadding = mContainer.getPaddingLeft() + mContainer.getPaddingRight();
  int drawableWidth = mContainer.getMeasuredWidth() - containerPadding;
  int dstWidth = drawableWidth - mPadding * 2;

  // 图片较小，不缩放
  if (dstWidth >= srcWidth)
  {
    drawable.setBitmap(srcBitmap);

    // 设置 drawable 区域
    int drawableHeight = srcHeight + 2 * mPadding;
    drawable.setBounds(0, 0, drawableWidth, drawableHeight);

    // 设置 bitmap 绘制区域
    int left = (drawableWidth - srcWidth) / 2;
    int top = mPadding;
    Rect rect = new Rect(left, top, left + srcWidth, top + srcHeight);
    drawable.setDstRect(rect);

  } else
  {
    // 图片缩放矩阵
    Matrix matrix = new Matrix();
    float scale = (float) dstWidth / srcWidth;
    matrix.postScale(scale, scale);

    // 转换 bitmap 为适合 drawable 的大小
    Bitmap dstBitmap = Bitmap.createBitmap(srcBitmap, 0, 0, srcWidth, srcHeight, matrix, false);
    drawable.setBitmap(dstBitmap);

    // 设置 drawable 区域
    int dstHeight = srcHeight * drawableWidth / srcWidth;
    int drawableHeight = dstHeight + 2 * mPadding;
    drawable.setBounds(0, 0, drawableWidth, drawableHeight);

    // 设置 bitmap 绘制区域
    int left = mPadding;
    int top = mPadding;
    Rect rect = new Rect(left, top, left + dstWidth, top + dstHeight);
    drawable.setDstRect(rect);
  }
}
static class MyDrawable extends BitmapDrawable
{
  Bitmap mBitmap;
  Rect mSrcRect;
  Rect mDstRect;

  public MyDrawable(Resources res, Bitmap bitmap)
  {
    super(res, bitmap);
  }

  public void setSrcRect(Rect srcRect)
  {
    mSrcRect = srcRect;
  }

  public void setDstRect(Rect dstRect)
  {
    mDstRect = dstRect;
  }

  @Override
  public void draw(Canvas canvas)
  {
    super.draw(canvas);

    // 绘制 bitmap
    if (mBitmap != null)
    {
      if (mSrcRect != null && mDstRect != null)
      {
        canvas.drawBitmap(mBitmap, mSrcRect, mDstRect, getPaint());
      } else if (mDstRect != null)
      {
        int width = mBitmap.getWidth();
        int height = mBitmap.getHeight();

        Rect srcRect = new Rect(0, 0, width, height);
        canvas.drawBitmap(mBitmap, srcRect, mDstRect, getPaint());
      } else
      {
        canvas.drawBitmap(mBitmap, 0, 0, getPaint());
      }
    }
  }

  public void setBitmap(Bitmap bitmap)
  {
    mBitmap = bitmap;
  }
}
```


## 参考链接
- [Drawable](http://developer.android.com/reference/android/graphics/drawable/Drawable.html)
