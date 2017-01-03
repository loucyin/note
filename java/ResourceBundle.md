# ResourceBundle

## IDEA 下面的 resources 文件夹

这个文件夹是什么鬼？IDEA 官网给的解释：

> Resources include properties files, images, DTDs, and XML files. These files are located under the Classpath of your application, and are usually loaded from the Classpath by means of the following methods:

> - ResourceBundle.getBundle() for the property files and resource bundles
> - loadResourceAsStream() for icons and other files

## 乱码的中文

- 在 resources 下新建一个 string.properties 、string_zh_CN.properties
- 通过下面的代码 :

  ```java
  ResourceBundle bundle = ResourceBundle.getBundle("string");
  System.out.println(bundle.getString(key));
  ```

  打印的竟然是乱码！

## 姿势不对，需要转码

```java
public static void main(String [] args){
    ResourceBundle bundle = ResourceBundle.getBundle("string");
    Enumeration<String> enumeration = bundle.getKeys();
    while (enumeration.hasMoreElements()) {
        String key = enumeration.nextElement();
        String value = changeCoding(bundle.getString(key));
        System.out.println("key : "+key+" , value : "+value);;
    }
}
public static String changeCoding(String str){
    if(str == null){
        return null;
    }

    String string = null;
    try {
        string = new String(str.getBytes("ISO-8859-1"),"utf-8");
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }
    return string;
}
```

## JavaWeb 下的 resources 咋用

## 参考链接

- [Resource Files](https://www.jetbrains.com/help/idea/2016.3/resource-files.html)
- [IntelliJ IDEA读取资源文件](http://www.linuxidc.com/Linux/2015-02/113325.htm)
