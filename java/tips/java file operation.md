# java 文件操作

## 文件的基本操作
```java
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
```java
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
```java
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
两种方式：一种是通过 ImageReader ，一种是通过 ImageIO ；ImageReader 根据文件名读取文件的信息，比较快速，但是当文件后缀与文件的实际类型不符时，会报异常，不支持 ICO 格式图片。ImageIO 是将文件读入，然后获取大小，比较耗时。
```java
public static void main(String[] args)
	{
		List<Long> imageReader = new ArrayList<>();
		List<Long> imageIo = new ArrayList<>();
		for (int i = 115; i < 120; i++)
		{
			String path = "E:\\image\\"+i+".jpg";
			// 新建文件
			File image = new File(path);
			// 计时开始
			long start = new Date().getTime();
			// 获取文件后缀名
			int index = path.lastIndexOf('.');
			String formatName = "jpg";
			if (index > 0)
			{
				formatName = path.substring(index + 1);
			}
			getImageSize(image, formatName);

			//计时结束，保存
			long end = new Date().getTime();
			imageReader.add(end - start);

			//计时开始
			start = new Date().getTime();

			getImageSize(image);

			//计时结束保存
			end = new Date().getTime();
			imageIo.add(end - start);
		}
		System.out.println("ImageReader:"+imageReader);
		System.out.println("ImageIO    :"+imageIo);
	}

	public static int[] getImageSize(File image, String formatName)
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
		return new int[] { 1200, 200 };
	}

	public static int[] getImageSize(File image)
	{
		InputStream is = null;
		BufferedImage src = null;
		int height = 0;
		int width = 0;
		try
		{
			is = new FileInputStream(image);
			src = javax.imageio.ImageIO.read(is);
			width = src.getWidth();
			height = src.getHeight();
			is.close();
		} catch (Exception e)
		{
			e.printStackTrace();
		}
		return new int[] { width, height };
	}
```
图越大，结果差异越大：
```
ImageReader:[81, 1, 0, 3, 20]
ImageIO    :[574, 323, 420, 463, 433]
```
## Thumbinal
使用第三方库：[coobird/thumbnailator](https://github.com/coobird/thumbnailator)
