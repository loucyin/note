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

## AspectJ

### 什么鬼
> - a seamless aspect-oriented extension to the Javatm programming language（一种基于Java平台的面向切面编程的语言）
- Java platform compatible（兼容Java平台，可以无缝扩展）
- easy to learn and use（易学易用）

aspectj 会在编译的时候把代码插入 class 文件，动态代理是在运行时插入代码。

### 例子
- 配置 gradle

  aspectj 插件需要依赖 aspectjVersion 属性，在引入插件之前要定义 aspectjVersion。

  ```groovy
  buildscript {
    repositories {
      maven {
        url "https://plugins.gradle.org/m2/"
      }
    }
    dependencies {
      classpath "gradle.plugin.aspectj:gradle-aspectj:0.1.6"
    }
  }

  ext {
      aspectjVersion = '1.8.4'
  }
  apply plugin: 'java'
  apply plugin: "aspectj.gradle"

  repositories {
      mavenCentral()
  }

  dependencies {
      testCompile group: 'junit', name: 'junit', version: '4.12'
      compile "org.aspectj:aspectjrt:${aspectjVersion}"
  }
  ```

- 定义 aspect

  新建文件 HelloAspect.aj 文件，加入如下代码。

  ```aspect
  package com.lcy.demo.aspectj;
  public aspect HelloAspect {
      pointcut HelloWorldPointCut() : execution(* com.lcy.demo.aspectj.Main.main(..));
      before() : HelloWorldPointCut(){
          System.out.println("Hello world");
      }
  }
  ```

- main

  ```java
  package com.lcy.demo.aspectj;
  public class Main {
      public static void main(String[] args) {

      }
  }
  ```

  运行 `gradle build` 命令后会生成 HelloAspect.class 与 Main.class 两个文件，查看 Main.class 会发现 Main 方法中被插入了一条语句。
  ```java
  public static void main(String[] args) {
      HelloAspect.aspectOf().ajc$before$com_lcy_demo_aspectj_HelloAspect$1$e54eb133();
  }
  ```

- 通过 java 定义 aspect

  ```java
  @Aspect
  public class TestAspect {
      @Before("execution(* com.lcy.demo.aspectj.Main.main(..))")
      public void sayHi(){
          System.out.println("hi");
      }
  }
  ```
  在执行 main 方法之前，打印 `hi`。


## 参考链接
- [Spring AOP 实现原理与 CGLIB 应用](https://www.ibm.com/developerworks/cn/java/j-lo-springaopcglib/?spm=5176.100239.blogcont7104.5.vj2Lm8)
- [java使用动态代理来实现AOP（日志记录）](http://www.cnblogs.com/tiantianbyconan/p/3336627.html)
- [AOP的底层实现-CGLIB动态代理和JDK动态代理](http://blog.csdn.net/dreamrealised/article/details/12885739)
- [跟我学aspectj之一 ----- 简介](http://blog.csdn.net/zl3450341/article/details/7673938#comments)
- [aspectj.gradle](https://plugins.gradle.org/plugin/aspectj.gradle)
