# aop

## 动态代理

### jdk 动态代理
- jdk 动态代理需要先定义统一的接口 service

  ```java
  public interface TestService {
      void add();
      void update();
  }
  ```

  service 实现：

  ```java
  public class TestServiceImpl implements TestService {
      @Override
      public void add() {
          System.out.println("TestServiceImpl add");
      }

      @Override
      public void update() {
          System.out.println("TestServiceImpl update");
      }
  }
  ```

- 定义 handler

  ```java
  public class MyInvocationHandler implements InvocationHandler{
      Object target;

      public MyInvocationHandler(Object target) {
          this.target = target;
      }

      @Override
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
          System.out.println("before invoke");
          Object o = method.invoke(target,args);
          System.out.println("after invoke");
          return o;
      }
  }
  ```

- 通过代理执行代码

  ```java
  public static void main(String[] args) {
      // 新建实例
      TestService testService = new TestServiceImpl();
      // 创建 handler
      MyInvocationHandler handler = new MyInvocationHandler(testService);
      // 生成代理
      TestService proxy = (TestService) Proxy.newProxyInstance(testService.getClass().getClassLoader(), testService.getClass().getInterfaces(),handler);
      // 通过代理执行
      proxy.add();
      proxy.update();
  }
  ```

### cglib 动态代理

cglib 不需要定义统一的接口。

- 创建 CglibInterceptor
  ```java
  public class CglibInterceptor implements MethodInterceptor{
      @Override
      public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
          System.out.println("before invoke");
          Object o = proxy.invokeSuper(obj,args);
          System.out.println("after invoke");
          return o;
      }
  }
  ```

- 创建工厂方法

  ```java
  public static TestService getInstance(CglibInterceptor proxy) {
      Enhancer enhancer = new Enhancer();
      enhancer.setSuperclass(TestServiceImpl.class);
      enhancer.setCallback(proxy);
      return (TestService) enhancer.create();
  }
  ```

- 通过代理执行代码
  ```java
  public static void main(String[] args) {
      TestService test = getInstance(new CglibInterceptor());
      test.add();
  }
  ```

TODO : 添加 AspectJ

## 参考链接
- [Spring AOP 实现原理与 CGLIB 应用](https://www.ibm.com/developerworks/cn/java/j-lo-springaopcglib/?spm=5176.100239.blogcont7104.5.vj2Lm8)
- [java使用动态代理来实现AOP（日志记录）](http://www.cnblogs.com/tiantianbyconan/p/3336627.html)
- [AOP的底层实现-CGLIB动态代理和JDK动态代理](http://blog.csdn.net/dreamrealised/article/details/12885739)
