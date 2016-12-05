# Class 使用

## 获取 PackageName

```java
private static String getPackageName(){
    return PackageDemo.class.getPackage().getName();
}
```

## 获取工程目录

```
String dir = System.getProperty("user.dir");
```

## 其他

- ### Test.class.getResource("")

   得到的是当前类FileTest.class文件的URI目录。不包括自己！
- ### Test.class.getResource("/")

   得到的是当前的classpath的绝对URI路径。
- ### Thread.currentThread().getContextClassLoader().getResource("")

   得到的也是当前ClassPath的绝对URI路径。
- ### Test.class.getClassLoader().getResource("")

   得到的也是当前ClassPath的绝对URI路径。
- ### ClassLoader.getSystemResource("")

   得到的也是当前ClassPath的绝对URI路径。 尽量不要使用相对于System.getProperty("user.dir")当前用户目录的相对路径，后面可以看出得出结果五花八门。
- ### new File("").getAbsolutePath()

  ## 参考链接

- ### [java获取当前类的绝对路径](http://tomfish88.iteye.com/blog/971255)
