# gradle 使用

## gradle 简介

百度百科对 gradle 的解释：

> Gradle是一个基于Apache Ant和Apache Maven概念的项目自动化建构工具。它使用一种基于Groovy的特定领域语言(DSL)来声明项目设置，抛弃了基于XML的各种繁琐配置。

gradle 脚本使用 Groovy 写的，什么是 Groovy 呢：

> Groovy 是 用于Java虚拟机的一种敏捷的动态语言，它是一种成熟的面向对象编程语言，既可以用于面向对象编程，又可以用作纯粹的脚本语言。使用该种语言不必编写过多的代码，同时又具有闭包和动态语言中的其他特性。

## 配置环境变量

- 下载安装 gradle
- 加入到环境变量：

  - 新建环境变量 GRADLE_HOME：<br>
    D:\Program Files\Android\Android Studio\gradle\gradle-2.14.1
  - 将 %GRADLE_HOME%\bin 添加到 Path 中。

## 简单的 groovy 语句

- 新建 build.gradle

  ```
  task groovy<<{}
  // 变量定义
  def foo = 6.5
  // 变量的使用
  println "foo has value: $foo"
  // 字符串中插入运算
  println "Let's do some math. 5 + 6 = ${5 + 6}"
  // 输出变量类型
  println "foo is of type: ${foo.class} and has value: $foo"
  foo = "a string"
  println "foo is now of type: ${foo.class} and has value: $foo"
  ```

- 执行

  ```
  gradle groovy
  ```

- 输出如下：

  ```
  foo has value: 6.5
  Let's do some math. 5 + 6 = 11
  foo is of type: class java.math.BigDecimal and has value: 6.5
  foo is now of type: class java.lang.String and has value: a string
  :groovy
  ```

- groovy 中可以直接使用 Java 语法：

  ```java
  class JavaGreeter {
    public void sayHello() {
        System.out.println("Hello Java!");
    }
  }
  JavaGreeter greeter = new JavaGreeter();
  greeter.sayHello();
  ```

## gradle task

- 创建任务

  ```
  project.task("myTask1")
  task("myTask2")
  task "myTask3"
  task myTask4
  myTask4.description = "This is what's shown in the task list"
  myTask4.group = "This is the heading for this task in the task list,"
  myTask4.doLast {println "Do this last"}
  myTask4.doFirst {println "Do this first"}
  myTask4.leftShift {println "Do this even more last"}
  myTask4 << {println "Do this last of all"}
  task myTask5 << {
    println "Here's how to declare a task and give it an action in one stroke"
  }
  task myTask6 {
    description "Here's a task with a configuration block"
    group "Some group"
    doLast {
        println "Here's the action"
    }
  }
  task myTask7 {
    description("Description") // Function call works
    //description "Description" // This is identical to the line above
    group = "Some group" // Assignment also works
    doLast { // We can also omit the parentheses, because Groovy syntax
        println "Here's the action"
    }
  }
  task myTask8(description: "Another description") << {
    println "Doing something"
  }
  ```

- 执行任务

  ```
  gradle myTask8
  ```

## gradle task 依赖关系

- dependsOn<br>
  执行 putOnShoes ，会先执行 putOnSocks。

  ```gradle
  task putOnSocks {
    doLast {
        println "Putting on Socks."
    }
  }
  task putOnShoes {
    dependsOn "putOnSocks"
    doLast {
        println "Putting on Shoes."
    }
  }
  ```

  ```
  task getReady {
    // Remember that when assigning a collection to a property, we need the
    // equals sign
    dependsOn = ["takeShower", "eatBreakfast", "putOnShoes"]
  }
  ```

- finalizedBy<br>
  执行 eatBreakfast ，完成后会执行 brushYourTeeth

  ```
  task eatBreakfast {
    finalizedBy "brushYourTeeth"
    doLast{
        println "Om nom nom breakfast!"
    }
  }
  task brushYourTeeth {
    doLast {
        println "Brushie Brushie Brushie."
    }
  }
  ```

- shouldRunAfter 执行 putOnFragrance ，会先执行 takeShower

  ```
  task takeShower {
    doLast {
        println "Taking a shower."
    }
  }
  task putOnFragrance {
    shouldRunAfter "takeShower"
    doLast{
        println "Smellin' fresh!"
    }
  }
  ```

## 使用 gradle 构建 HelloWorld

- 新建一个 build.gradle ,加入如下代码：

  ```
  apply plugin: 'java'
  ```

- 新建目录 src/main/java<br>
  这是 gradle 默认的 java 目录，不这样写 gradle 找不到 java 文件。 添加 /demo/HelloWorld.java

  ```
  package demo;
  public class HelloWorld{
    public static void main(String[] args) {
        System.out.println("Hello World");
    }
  }
  ```

- 编译

  ```
  gradle build
  ```

  build 会执行下面的 task:

  ```
  :compileJava
  :processResources UP-TO-DATE
  :classes
  :jar
  :assemble
  :compileTestJava UP-TO-DATE
  :processTestResources UP-TO-DATE
  :testClasses UP-TO-DATE
  :test UP-TO-DATE
  :check UP-TO-DATE
  :build
  BUILD SUCCESSFUL
  ```

- 运行<br>
  build 之后，会在当前目录生成 build 文件夹，classes 文件在 build/classes/main/demo 文件夹下面。 切换目录到 build/classes/main，执行：

  ```
  java demo/HelloWorld
  ```

- 另一种运行方法

  ```
  apply plugin: 'java'
  task execute(type: JavaExec){
    main = "demo.HelloWorld"
    classpath = sourceSets.main.runtimeClasspath
  }
  ```

  执行：

  ```
  gradle execute
  ```

  输出如下：

  ```
  :compileJava
  :processResources UP-TO-DATE
  :classes
  :execute
  Hello World
  BUILD SUCCESSFUL
  ```

## gradle 使用 maven 仓库

- 在工程下的 build.gradle 中添加 maven 仓库：

  ```groovy
  allprojects {
    repositories {
        jcenter()
        mavenCentral()
    }
  }
  ```

- maven 配置

  ```xml
  <dependency>
  <groupId>com.google.zxing</groupId>
  <artifactId>core</artifactId>
  <version>(the current version)</version>
  </dependency>
  ```

- 转换为 gradle 配置

  ```groovy
  compile 'com.google.zxing:core:3.3.0'
  ```

## gradle 构建 Java web 项目

### web 项目的目录结构

|-WebRoot<br>
| -WEB-INF<br>
| -lib<br>
| -classes

### gradle 配置

- 需要将编译的 class 文件复制到 web 项目下
- 需要将 lib 复制到 web 项目下
- 配置 web.xml
- 配置部署环境

```groovy
build{
    doLast{
        delete 'webapp/WEB-INF/classes'
        copy{
            from 'build/classes/main'
            into 'webapp/WEB-INF/classes'
        }
        copy{
            from configurations.runtime
            into 'webapp/WEB-INF/lib'
        }
    }
}
```

## gradle 生成可执行 jar 包

在 build.gradle 中加入如下代码：

```groovy
jar {

    archiveName = "init.jar"

    from {
        configurations.runtime.collect {
            it.isDirectory() ? it : zipTree(it)
        }
        configurations.compile.collect {
            it.isDirectory() ? it : zipTree(it)
        }
    }

    manifest {
        attributes 'Main-Class': 'com.lcy.demo.InitProjectUtil'
    }
    exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
}
```

## 参考链接

- [百度百科 gradle](http://baike.baidu.com/link?url=irOH1pxXeqYPRZ7pofxDiBZ7I37nvpzzS75qfkXYQ3FRGuUQE5BhZ11xRzwou2q7Pi9K525JkZPhwaV9Fai8PK)
- [Gradle入门系列（1）：简介](http://blog.jobbole.com/71999/)
- [使用Gradle构建Java项目](http://blog.csdn.net/jueane/article/details/50476960)
- [ud867: Gradle for Android and Java](https://github.com/udacity/ud867)
- [Gradle 视频教程](https://cn.udacity.com/course/gradle-for-android-and-java--ud867)
- [gradle user guide](https://docs.gradle.org/current/userguide/userguide.html)
- [Maven实战（六）----Gradle，构建工具的未来？](http://www.infoq.com/cn/news/2011/04/xxb-maven-6-gradle)
