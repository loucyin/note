# 使用Dagger 2进行依赖注入

## java dagger2

### 依赖管理
```groovy
buildscript {
    repositories {
        maven {
            url "https://plugins.gradle.org/m2/"
        }
    }
    dependencies {
        classpath "net.ltgt.gradle:gradle-apt-plugin:0.10"
    }
}
apply plugin: 'java'
apply plugin: "net.ltgt.apt"

dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.12'
    compile 'com.google.dagger:dagger:2.11'
    apt 'com.google.dagger:dagger-compiler:2.11'
}

sourceSets {
    main.java.srcDirs += [file("$buildDir/generated/source/apt/main")]
    test.java.srcDirs += [file("$buildDir/generated/source/apt/test")]
}
```

注意事项
- dagger2 注入是在编译期间完成的，会生成中间代码，需要把中间代码加入到 srcDirs 中；
- 加入 apt 插件

### modules
```java
@Module
public class ServiceModule {
    @Provides
    AlertListService alertListService(){
        return RestClient.getService(AlertListService.class);
    }
}
```

### inject

```java
public class InjectTest {
    @Inject
    protected AlertListService service;

    public AlertListService getService() {
        return service;
    }

    public void setService(AlertListService service) {
        this.service = service;
    }
}
```

```java
public class Test {
    private AlertListService listService;
    @Inject
    public Test(AlertListService listService) {
        this.listService = listService;
    }

    public AlertListService getListService() {
        return listService;
    }

    public void setListService(AlertListService listService) {
        this.listService = listService;
    }
}
```

### component
```java
@Component(modules = ServiceModule.class)
public interface ServiceComponent {
    Test test();
    void inject(InjectTest test);
}
```

### 使用
```java
public static void main(String[] args) {
    ServiceComponent component = DaggerServiceComponent.create();
    InjectTest injectTest = new InjectTest();
    component.inject(injectTest);
    System.out.println(injectTest.getService());
    Test test = component.test();
    System.out.println(test.getListService());
}
```

## kotlin dagger2

### 依赖配置
```groovy
buildscript {
    ext.kotlin_version = '1.1.1'

    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}
apply plugin: 'kotlin'
apply plugin: 'kotlin-kapt'
dependencies {
    compile "org.jetbrains.kotlin:kotlin-stdlib-jre8:$kotlin_version"
    compile group: 'io.reactivex.rxjava2', name: 'rxkotlin', version: '2.0.3'
    compile 'com.google.dagger:dagger:2.11'
    kapt 'com.google.dagger:dagger-compiler:2.11'
}
sourceSets {
    main.java.srcDirs += [file("$buildDir/generated/source/kapt/main")]
}
```

### modules
```kotlin
@Module
class ServiceModule {
    @Provides
    fun alertListService(): AlertListService {
        return RestClient.getService(AlertListService::class.java)
    }
}
```

### inject
- 属性注入
```kotlin
open class BaseTest {
    @Inject
    lateinit var service:AlertListService
}
```

- 构造器注入
```kontlin
class ServiceTest @Inject constructor(val listService: AlertListService){

}
```

### component
```kotlin
@Component(modules = arrayOf(ServiceModule::class))
interface ServiceComponent {
    fun test(): ServiceTest
    fun inject(baseTest: BaseTest)
}
```

### 使用
```kontlin
fun main(args: Array<String>) {
    val component = DaggerServiceComponent.create();
    val test = component.test();
    println(test.listService)
    val injectTest = BaseTest()
    component.inject(injectTest)
    println(injectTest.service)
}
```

## 参考链接

- [使用Dagger 2进行依赖注入](http://codethink.me/2015/08/06/dependency-injection-with-dagger-2/)
- [Android：dagger2让你爱不释手-基础依赖注入框架篇](http://www.jianshu.com/p/cd2c1c9f68d4)
