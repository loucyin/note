## 配置一个 JFinal 项目
- 创建一个 Web Dynamic Project
Default Output Folder 与 Content directory 要相对应。
> 此处的 Default out folder 必须要与 WebRoot/WEB-INF/classes 目录完全一致才可
以使用 JFinal 集成的 Jetty 来启动项目。

- 配置

## Jfinal Post 限制上传大小为 10M
解决方法：
1. 重写 JFinalConfig 中的 configConstant 方法，设置 maxPostSize。
```java
@Override
	public void configConstant(Constants arg0)
	{
		arg0.setMaxPostSize(100*Const.DEFAULT_MAX_POST_SIZE);
	}
```  
- 通过 Controller 获取文件或者文件列表时，设置 maxPostSize。
```java  
mController.getFile(uploadPath, maxPostSize);
mController.getFile(uploadPath, maxPostSize, encoding);
mController.getFiles(uploadPath, maxPostSize);
mController.getFiles(uploadPath, maxPostSize, encoding);  
```
