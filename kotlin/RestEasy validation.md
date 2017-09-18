# Resteasy Validation

## 依赖

```groovy
compile "org.jboss.resteasy:resteasy-validator-provider-11:$restEasyVersion"
```

## 代码

Bean:

```kotlin
data class User (
        @field:NotNull(message = "用户名不能为空") var name:String? = null,
        @field:Min(value = 0,message = "年龄最小值为 0") var age:Int = 0)
```

**注意要使用 @field 进行注解，否则 @Valid 注解不起作用**

接口：

```kotlin
@POST
@Path("add")
@Valid
fun addUser( @Valid user:User):User{
    println(user.name)
    return user
}
```

Exception Handler

```
@Provider
@Consumes(MediaType.APPLICATION_JSON+";charset=utf-8")
@Produces(MediaType.APPLICATION_JSON+";charset=utf-8")
class ValidatorExceptionMapper :ExceptionMapper<ResteasyViolationException>{
    override fun toResponse(exception: ResteasyViolationException): Response {
        val map = HashMap<String,String>()
        exception.violations.forEach {
            map.put(it.path,it.message)
        }
        return Response.ok(map,MediaType.APPLICATION_JSON_TYPE.withCharset("utf-8"))
                .build()
    }
}
```

ExceptionMapper 的调用位置 org.jboss.resteasy.core.ExceptionHandler.executeExactExceptionMapper：

```java
public Response executeExactExceptionMapper(Throwable exception) {
    ExceptionMapper mapper = (ExceptionMapper)this.providerFactory.getExceptionMappers().get(exception.getClass());
    if(mapper == null) {
        return null;
    } else {
        this.mapperExecuted = true;
        return mapper.toResponse(exception);
    }
}
```

必须定义 ExceptionMapper<ResteasyViolationException> 类型的 ExceptionMapper 才能起作用。

## 参考链接
- [Kotlin & Spring boot 使用@Valid校验无效解决方法](https://acehjm.github.io/2017/04/12/Kotlin-Spring-boot-%E4%BD%BF%E7%94%A8-Valid%E6%A0%A1%E9%AA%8C%E6%97%A0%E6%95%88%E8%A7%A3%E5%86%B3%E6%96%B9%E6%B3%95/)
