# RestEasy form 表单文件名乱码

## 依赖问题

使用 resteasy 上传表单需要依赖：

```xml
<dependency>
  <groupId>org.jboss.resteasy</groupId>
  <artifactId>resteasy-multipart-provider</artifactId>
  <version>3.1.2.Final</version>
</dependency>
```

multipart 解析表单数据依赖于：

```xml
<dependency>
    <groupId>org.apache.james</groupId>
    <artifactId>apache-mime4j</artifactId>
</dependency>
```

版本号为 ： 0.6

## 解析

通过单步调试，发现解析 header 的代码为：

```java
public static String decode(ByteSequence byteSequence) {
    return decode(CharsetUtil.US_ASCII, (ByteSequence)byteSequence, 0, byteSequence.length());
}
```

默认使用 US_ASCII ，而且没法设置其他字符格式。

**解决方法**

替换版本库：

```xml
<dependency>
  <groupId>org.jboss.resteasy</groupId>
  <artifactId>resteasy-multipart-provider</artifactId>
  <version>3.1.4.Final</version>
  <exclusions>
    <exclusion>
      <groupId>org.apache.james</groupId>
      <artifactId>apache-mime4j</artifactId>
    </exclusion>
  </exclusions>
</dependency>
<dependency>
  <groupId>org.apache.james</groupId>
  <artifactId>apache-mime4j</artifactId>
  <version>0.6.2-SNAPSHOT</version>
</dependency>
```

由于 apache-mime4j 高版本 0.7 0.8 与 0.6 不兼容，所以只能自己下载 0.6 版本的源码编译 jar 包。

将 ContentUtil.java 文件中默认的编码方式由 `CharsetUtil.US_ASCII` 改为 `CharsetUtil.UTF_8`


## 其他

解析 form 表单参数的时候，可以通过下面的方式设置默认字符集：

```java
httpRequest.setAttribute(InputPart.DEFAULT_CHARSET_PROPERTY,"utf-8");
```

RestEasy 拦截器：

```java
@Provider
public class MyFilter implements ContainerRequestFilter{
    @Override
    public void filter(ContainerRequestContext containerRequestContext) throws IOException {
        if(containerRequestContext instanceof PostMatchContainerRequestContext){
            ((PostMatchContainerRequestContext) containerRequestContext).getHttpRequest().setAttribute(InputPart.DEFAULT_CONTENT_TYPE_PROPERTY,"text/html;charset=utf-8");
            ((PostMatchContainerRequestContext) containerRequestContext).getHttpRequest().setAttribute(InputPart.DEFAULT_CHARSET_PROPERTY,"utf-8");
        }
    }
}
```
