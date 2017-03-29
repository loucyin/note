# spring 基础

spring 最主要的特点有两个： **IOC** 和 **AOP** 。

## 创建对象的三种方式

- 手动创建 new
- 工厂模式 get
- 注入 set

## IOC
IOC (Inversion of Control) 控制反转：

>本来是由应用程序管理的对象之间的依赖关系，现在交给了容器管理，这就叫控制反转，即交给了IOC容器，Spring的IOC容器主要使用DI方式实现的。不需要主动查找，对象的查找、定位和创建全部由容器管理。

依赖注入的两种方式：

- 构造方法注入
- setter 注入

IOC 容器：
- BeanFactory 容器
- ApplicationContext 容器

Spring 配置元数据
- 基于 XML 的配置文件。
- 基于注解的配置
- 基于 Java 的配置

### bean
作用域  | 描述
--|--
singleton	| 该作用域将 bean 的定义的限制在每一个 Spring IoC 容器中的一个单一实例(默认)。
prototype	| 该作用域将单一 bean 的定义限制在任意数量的对象实例。
request	| 该作用域将 bean 的定义限制为 HTTP 请求。只在 web-aware Spring ApplicationContext 的上下文中有效。
session	| 该作用域将 bean 的定义限制为 HTTP 会话。 只在web-aware Spring ApplicationContext的上下文中有效。
global-session	| 该作用域将 bean 的定义限制为全局 HTTP 会话。只在 web-aware Spring ApplicationContext 的上下文中有效。

### 注解
序号	| 注解 & 描述
--|--
1 |	@Required<br>@Required 注解应用于 bean 属性的 setter 方法。
2 |	@Autowired<br>@Autowired 注解可以应用到 bean 属性的 setter 方法，非 setter 方法，构造函数和属性。
3 |	@Qualifier<br>通过指定确切的将被连线的 bean，@Autowired 和 @Qualifier 注解可以用来删除混乱。

注解 | 描述
--|--
@Service | 用于标注业务层组件
@Controller | 用于标注控制层组件（如struts中的action）
@Repository | 用于标注数据访问组件，即DAO组件
@Component | 泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。

4 个注解功能等效，但是在命名上对功能做了区分。

## AOP


## 参考链接
- [Java回顾之Spring基础](http://www.cnblogs.com/wing011203/archive/2013/05/15/3078849.html)
- [谈谈对Spring IOC的理解](http://blog.csdn.net/qq_22654611/article/details/52606960)
- [Spring-- IOC容器详解](http://www.open-open.com/lib/view/open1338175365089.html)
- [Quick Start](https://projects.spring.io/spring-framework/#quick-start)
- [Spring注解@Component、@Repository、@Service、@Controller区别](http://blog.csdn.net/zhang854429783/article/details/6785574)
