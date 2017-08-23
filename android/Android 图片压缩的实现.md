# 图片压缩

## 思路
- 按比例缩小图片的像素
- 降低图片的质量
- 裁剪

## 按比例缩小图片的像素
- BitmapFactory.Options
  - 使用 inJustDecodeBounds 成员变量，可以在不加载 bitmap 的情况下，获取 bitmap 的信息。
> If set to true, the decoder will return null (no bitmap), but the out... fields will still be set, allowing the caller to query the bitmap without having to allocate the memory for its pixels.

  - 使用 inSampleSize 控制采样大小
  inSampleSize > 1 的时候，图片会被缩小为原来的 1/inSampleSize^2 ;inSampleSize <= 1 时，inSampleSize = 1。它无法精准的控制 bitmap 的尺寸。
- Bitmap.createScaledBitmap()
  可以通过 Bitmap.createScaledBitmap() 方法获取到想要的尺寸。
- 达到按比例缩放的目的
  1. 计算出 inSampleSize 使读取到的图片尽量与需要的尺寸相近；
  2. 利用 Bitmap.createScaledBitmap() 方法，获取到需要的尺寸。
  3. 利用 Bitmap.compress() 方法，进行质量压缩。
  ```java
  /**
   * 默认目标图的宽度为 800 ，当 reqWidth reqHeight 都为 0 时，使用默认宽度；
   * 有一个为 0 时，使用不为 0 的计算缩放比例，和采样系数；
   * 当都不为 0 时，先计算，在选择合适的值。
   * 当原图尺寸，小于目标尺寸时，不进行缩放，只进行压缩；否则，先缩小尺寸，在压缩。
   *
   * @param srcPath   原图路径
   * @param dstPath   目标图路径
   * @param reqWidth  目标宽度
   * @param reqHeight 目标高度
   * @param quality   生成图片的质量 0-100
   * @return false 压缩失败，true 压缩成功
   */
  public static boolean compressImage(String srcPath, String dstPath, int reqWidth, int reqHeight, int quality)
  {
      float defaultWidth = 800.f;

      // 获取 bitmap 信息，但不加载 bitmap
      BitmapFactory.Options options = new BitmapFactory.Options();
      options.inJustDecodeBounds = true;
      BitmapFactory.decodeFile(srcPath, options);
      int width = options.outWidth;
      int height = options.outHeight;

      if (width == 0 || height == 0)
      {
          return false;
      }

      // 计算采样比例
      int sample = 1;
      if (reqWidth != 0 && reqHeight != 0)
      {
          sample = Math.min(width / reqWidth, height / reqHeight);
      } else if (reqWidth != 0)
      {
          sample = width / reqWidth;
      } else if (reqHeight != 0)
      {
          sample = height / reqHeight;
      } else
      {
          sample = width / 800;
      }

      // 加载 bitmap
      options.inJustDecodeBounds = false;
      options.inSampleSize = sample;
      Bitmap bitmap = BitmapFactory.decodeFile(srcPath, options);

      width = options.outWidth;
      height = options.outHeight;

      // 计算缩放比例
      float scale;
      if (reqWidth != 0 && reqHeight != 0)
      {
          scale = Math.min((float) reqWidth / width, (float) reqHeight / height);
      } else if (reqWidth != 0)
      {
          scale = (float) reqWidth / width;
      } else if (reqHeight != 0)
      {
          scale = (float) reqHeight / height;
      } else
      {
          scale = defaultWidth / width;
      }

      // 图片缩放
      if (scale < 1.0f)
      {
          Matrix matrix = new Matrix();
          matrix.postScale(scale, scale);
          bitmap = Bitmap.createBitmap(bitmap, 0, 0, width, height, matrix, false);
      }

      // 保存到目的地址
      ByteArrayOutputStream outputStream = new ByteArrayOutputStream();

      quality = quality < 0 ? 0 : quality;
      quality = quality > 100 ? 100 : quality;
      bitmap.compress(Bitmap.CompressFormat.JPEG, quality, outputStream);
      try
      {
          FileOutputStream fos = new FileOutputStream(new File(dstPath));
          fos.write(outputStream.toByteArray());
          fos.flush();
          fos.close();
      } catch (IOException e)
      {
          e.printStackTrace();
          return false;
      }
      return true;
  }
  ```

## 参考链接
- [ Android图片压缩技巧](http://blog.csdn.net/fengyuzhengfan/article/details/41759835)
- [BitmapFactory.Options](http://developer.android.com/reference/android/graphics/BitmapFactory.Options.html#inJustDecodeBounds)
- [Android how to scale an image with BitmapFactory Options](http://stackoverflow.com/questions/9360976/android-how-to-scale-an-image-with-bitmapfactory-options)
