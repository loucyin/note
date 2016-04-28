## 文件的基本操作
```
//创建文件
File file = new File(fileName);

//判断文件是否存在
if (file.exists())
{
    file.delete();
}

//创建目录
File dir = new File(path);

// 目录不存在创建新目录
File dir = new File(path);
if (!dir.exists())
{
    dir.mkdirs();
}

//获取目录下的文件名列表
String[] fileList = dir.list();
```
## 文件的读写
- 通过FileWriter写入
```
public static boolean writeStringToFile(String content, String fileName)
	{
		File file = new File(fileName);
		if (file.exists())
		{
			file.delete();
		}
		FileWriter writer = null;
		try
		{
			writer = new FileWriter(file);
			writer.write(content);
		} catch (Exception e)
		{
			e.printStackTrace();
			return false;
		} finally
		{
			if (writer != null)
			{
				try
				{
					writer.flush();
					writer.close();
				} catch (IOException e)
				{
					e.printStackTrace();
				}
			}
		}
		return true;
	}
```
- 通过stream读写
```
    InputStream inputStream = null;
		OutputStream outputStream = null;
		int len = 0;
		byte[] b = new byte[1024];

		try
		{
			// 保存文件
			File file = new File(path);
			inputStream = request.getInputStream();
			outputStream = new FileOutputStream(file);
			while ((len = inputStream.read(b, 0, 1024)) != -1)
			{
				outputStream.write(b, 0, len);
			}
		} catch (Exception e)
		{
			e.printStackTrace();
			writer.println(ResponseData.getResponse(ResponseData.SERVER_ERR));
		} finally
		{
			if (inputStream != null)
			{
				inputStream.close();
			}
			if (outputStream != null)
			{
				outputStream.close();
			}
		}
```
## 获取图片的大小
```
//支持 jpg png gif bmp 格式，不支持 ico 格式
public static int[] getImageSize(File image,String formatName)
	{
		try
		{
			Iterator<ImageReader> readers = ImageIO.getImageReadersByFormatName(formatName);
			ImageReader reader = (ImageReader) readers.next();
			ImageInputStream iis = ImageIO.createImageInputStream(image);
			reader.setInput(iis, true);

			return new int[] { reader.getWidth(0), reader.getHeight(0) };
		} catch (IOException e)
		{
			e.printStackTrace();
		}
		return null;
	}
```
## Thumbinal
使用第三方库：[coobird/thumbnailator](https://github.com/coobird/thumbnailator)
