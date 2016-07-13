## 资源文件夹
### res/raw and assets
- 相同点  
两者目录下的文件在打包后会原封不动的保存在apk包中，不会被编译成二进制。
- 不同点  
  1. res/raw 中的文件会被映射到 R.Java 文件中，访问的时候直接使用资源 ID 即R.id.filename；assets 文件夹下的文件不会被映射到 R.java 中，访问的时候需要 AssetManager 类。
  - res/raw不可以有目录结构，而assets则可以有目录结构，也就是assets目录下可以再建立文件夹

### 用法
- 操作 res/raw
```java
InputStream is = getResources().openRawResource(R.id.filename);  
```
- 操作 assets
```java
AssetManager am = getAssets();  
InputStream is = am.open("filename");  
```
## 参考链接
- [Android中资源文件夹res/raw和assets的使用](http://blog.csdn.net/zuolongsnail/article/details/6444806)
- [Android中assets目录和res/raw目录的异同和使用场景](http://blog.163.com/nsm0701@126/blog/static/16019503820138253931334/)
