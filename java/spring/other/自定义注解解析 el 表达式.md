# 自定义注解解析 el 表达式

## 注解

```kotlin
@Retention(AnnotationRetention.RUNTIME)
@Target(AnnotationTarget.FUNCTION)
annotation class ElTest(val value:String)
```

ElTest 注解用于指定 el 表达式。

## Aspect

```kotlin
@Service
@Aspect
class CustomerElAspect{
   val parser = SpelExpressionParser()

   @Around("(@annotation(com.gosun.el.demo.ElTest) || @within(com.gosun.el.demo.ElTest)) && @annotation(elTest)")
   fun getEl(joinpoint:ProceedingJoinPoint,elTest: ElTest):Any?{
       // 获取 method 签名
       val signature = joinpoint.signature as MethodSignature
       // 生成 context
       val context = MethodBasedEvaluationContext(joinpoint.`this`,signature.method,joinpoint.args,DefaultParameterNameDiscoverer())
       // 解析 el 表达式
       val expression = parser.parseExpression(elTest.value)
       // 从 el 表达式中获取相应的值
       println(expression.getValue(context))
       return joinpoint.proceed()
   }
}
```

## 测试一下

```kotlin
@SpringBootApplication
@EnableAspectJAutoProxy
@RestController
class ElApp {
    @PostMapping("users")
    @ElTest("#user.name")
    fun addUser(@RequestBody user: User) :User{
        return user
    }
}

fun main(args: Array<String>) {
    runApplication<ElApp>()
}

data class User(
        val name: String,
        val age: Int
)
```
