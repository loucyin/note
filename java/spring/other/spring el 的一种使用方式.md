# Spring el 的一种使用方式

通过 el 表达式获取 bean 的属性。

## 定义一个注解

```kotlin
@Retention(AnnotationRetention.RUNTIME)
@Target(AnnotationTarget.FUNCTION)
annotation class ElTest(val value:String)
```

通过注解传入 el 表达式。

## 定义 aspect

```kotlin
@Service
@Aspect
class CustomerElAspect{
    // 定义 parser
    val parser = SpelExpressionParser()

    @Around("(@annotation(com.gosun.el.demo.ElTest) || @within(com.gosun.el.demo.ElTest)) && @annotation(elTest)")
    fun getEl(joinpoint:ProceedingJoinPoint,elTest: ElTest):Any?{
        val signature = joinpoint.signature as MethodSignature
        // 生成 context
        val context = MethodBasedEvaluationContext(joinpoint.`this`,signature.method,joinpoint.args,DefaultParameterNameDiscoverer())
        // 生成表达式对象
        val expression = parser.parseExpression(elTest.value)
        // 获取相关属性
        println(expression.getValue(context))
        return joinpoint.proceed()
    }
}
```

## 测试代码

测试 service

```kotlin
@Component
class HelloService {
    @ElTest("#user.name")
    fun sayHello(user: User){
        println("hello")
    }
}
```

测试代码

```kotlin
@ComponentScan("com.spring.el.demo")
@EnableAspectJAutoProxy
@ContextConfiguration(classes = [AnnotationTest::class])
@RunWith(SpringRunner::class)
class AnnotationTest {

    @Autowired
    lateinit var helloService: HelloService

    @Test
    fun test(){
        helloService.sayHello(User("xiaoming",12))
    }

}
```

## 参考链接

- [Spring Expression Language](https://docs.spring.io/spring/docs/5.0.9.RELEASE/spring-framework-reference/core.html#expressions)
