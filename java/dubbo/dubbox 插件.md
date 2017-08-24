# dubbox 插件
## dubbox exception 处理

### 需求场景
对于客户端请求，缺乏参数的情况，可以抛出自定义异常，捕获后，封装成自定义的数据格式。

### 自定义 Exception 处理
> Dubbo的REST也支持JAX-RS标准的ExceptionMapper，可以用来定制特定exception发生后应该返回的HTTP响应。

- CustomException ，异常包含一个异常代码，message 使用 Exception 的 message
```java
public class CustomException extends Exception{
    private int code;

    public CustomException(int code,String message) {
        super(message);
        this.code = code;
    }

    public int getCode() {
        return code;
    }

    public void setCode(int code) {
        this.code = code;
    }
}
```

- ResponseData
```java
public class ResponseData{
  private int code;
  private String msg;

  public ResponseData(int code, String msg) {
      this.code = code;
      this.msg = msg;
  }

  public int getCode() {
      return code;
  }

  public void setCode(int code) {
      this.code = code;
  }

  public String getMsg() {
      return msg;
  }

  public void setMsg(String msg) {
      this.msg = msg;
  }
}
```

- CustomExceptionMapper，将 CustomException 转化为 Response
```java
import org.jboss.resteasy.specimpl.ResponseBuilderImpl;
import javax.ws.rs.core.Response;
import javax.ws.rs.ext.ExceptionMapper;
public class CustomExceptionMapper implements ExceptionMapper<CustomException> {
    @Override
    public Response toResponse(CustomException exception) {
        System.out.println("exception handle");
        Response response = new ResponseBuilderImpl()
                .entity(new ResponseData(exception.getCode(),exception.getMessage()))
                .build();
        return response;
    }
}
```
**注意事项**

  org.jboss.resteasy.specimpl.ResponseBuilderImpl 中 entity 必须是一个标准的 java bean，否则会报异常。

- 在 dubbo 的 rest 协议中添加 extension
```xml
<dubbo:protocol name="rest" server="tomcat" port="8080" contextpath="api" extension="com.lcy.demo.CustomExceptionMapper"/>
```

## dubbox 的日期序列化
依赖关系：
- dubbox 通过 RESTEasy 提供 rest 服务，RESTEasy 是 JAX-RS 规范的完整实现。
- JAX-RS 通过 javax.ws.rs.ext.Providers 接口对外提供服务。
- dubbox 可以通过 rest 协议中的 extension 配置 Providers 的相关服务。

### 实现

- JacksonConfig 实现
```java
public class JacksonConfig implements ContextResolver<ObjectMapper> {
    private  final ObjectMapper mapper = new ObjectMapper();

    public JacksonConfig() {
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
        mapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd"));
    }

    @Override
    public ObjectMapper getContext(Class<?> aClass) {
        return mapper;
    }
}
```

- 配置 dubbox rest 协议的 extension

```xml

```

### 参考链接
- [在Dubbo中开发REST风格的远程调用](https://dangdangdotcom.github.io/dubbox/rest.html)
