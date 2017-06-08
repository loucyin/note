# spring aop 自定义注解拦截器

## 方法级拦截器
### 注解接口
```java
import java.lang.annotation.*;
@Target({ ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface CheckPermission {
}
```

### 拦截器
```java
@Component
@Aspect
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

#### @Before @After @Around
- @Before 在方法执行之前调用
- @After 在方法执行之后调用
- @Around 可以控制方法的执行


### 配置自动代理
```xml
<aop:aspectj-autoproxy/>
```

## 类级拦截器

### 修改注解接口 Target
```java
import java.lang.annotation.*;
@Target({ ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface CheckPermission {
}
```

### 修改拦截器 Pointcut
```java
@Component
@Aspect
public class CheckPermissionAspect {

    @Around("@annotation(com.lcy.demo.aop.CheckPermission) || @within(com.lcy.demo.aop.CheckPermission)")
    public Object doBefore(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println(joinPoint.getSignature().getName());
        Object[] args = joinPoint.getArgs();
        System.out.println(Arrays.asList(args));
        return joinPoint.proceed();
    }
}
```

## 拦截器中获取注解接口
```java
@Component
@Aspect
public class CheckPermissionAspect {
    @Around("@annotation(com.lcy.demo.aop.CheckPermission) || @within(com.lcy.demo.aop.CheckPermission) && @annotation(checkPermission)")
    public Object doBefore(ProceedingJoinPoint joinPoint) throws Throwable {
        System.out.println(joinPoint.getSignature().getName());
        Object[] args = joinPoint.getArgs();
        System.out.println(Arrays.asList(args));
        return joinPoint.proceed();
    }
}
```

## 参考链接
- [@AspectJ pointcut for all methods of a class with specific annotation](https://stackoverflow.com/questions/2011089/aspectj-pointcut-for-all-methods-of-a-class-with-specific-annotation)
