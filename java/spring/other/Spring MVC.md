# Spring MVC

## 统一异常处理
Spring 使用 `@ControllerAdvice` 注解拦截异常并统一处理。

示例拦截 `@RequestParam` required 为 true 但是 request 中没有传递相关参数，产生的异常。

```
@ControllerAdvice
class MyControllerAdvice {
    @ResponseBody
    @ExceptionHandler(MissingServletRequestParameterException::class)
    fun paramErrorHandler(e:MissingServletRequestParameterException):ResponseData{
        return ResponseData(0,e.message)
    }
}
```

##

## 参考链接
- [Spring Boot 系列（八）@ControllerAdvice 拦截异常并统一处理](https://www.cnblogs.com/magicalSam/p/7198420.html)
