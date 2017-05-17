# spring 自定义注解拦截器

## 注解接口
```java
import java.lang.annotation.*;
@Target({ ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface CheckPermission {
}
```

## 拦截器
```java
public class CheckPermissionAspect {

    @Pointcut("@annotation(com.lcy.demo.aop.CheckPermission)")
    public void methodAspect(){

    }

    @Around("methodAspect()")
    public Object doBefore(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println(joinPoint.getSignature().getName());
        Object[] args = joinPoint.getArgs();
        System.out.println(Arrays.asList(args));
        return joinPoint.proceed();
    }
}
```

### @Before @After @Around
- @Before 在方法执行之前调用
- @After 在方法执行之后调用
- @Around 可以控制方法的执行


## 配置自动代理
```xml
<aop:aspectj-autoproxy/>
```
