# Mybatis

## 配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <properties resource="config.properties">
        <property name="username" value="dev_user"/>
        <property name="password" value="F2Fa3!33TYyg"/>
    </properties>
    <typeAliases>
        <package name="com.lcy.demo.mybatis" />
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
              <property name="driver" value="${driver}"/>
              <property name="url" value="${url}"/>
              <property name="username" value="${username}"/>
              <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="UserMapper.xml"/>
    </mappers>
</configuration>
```

**注意的问题**

### 配置文件中 configration 的子节点是有顺序要求的。
1. properties 属性
- settings 设置
- typeAliases 类型命名
- typeHandlers 类型处理器
- objectFactory 对象工厂
- plugins 插件
- environments 环境
- environment 环境变量
- transactionManager 事务管理器
- dataSource 数据源
- databaseIdProvider 数据库厂商标识
- mappers 映射器

### resource 属性
resource 属性设置的是文件路径，以 classpath 为根目录的路径，不能使用 `com.demo.Mapper.xml` 的形式，要使用 `com/demo/Mapper.xml`的形式。

### 配置 properties 的读取顺序
> 如果属性在不只一个地方进行了配置，那么 MyBatis 将按照下面的顺序来加载：
- 在 properties 元素体内指定的属性首先被读取。
- 然后根据 properties 元素中的 resource 属性读取类路径下属性文件或根据 url 属性指定的路径读取属性文件，并覆盖已读取的同名属性。
- 最后读取作为方法参数传递的属性，并覆盖已读取的同名属性。

## SQL 文件
创建 UserMapper.xml，UserMapper.xml 相当于生成了 UserMapper.class ,namespace 相当于 UserMapper 的包路径。
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.lcy.demo.UserMapper">
    <select id="selectUser" resultType="com.lcy.demo.User">
        select * from user where id = #{id};
    </select>
</mapper>
```

## 使用
- 通过 config.xml 初始化 SqlSessionFactory
- 通过 SqlSessionFactory 获取到 SqlSession
- 通过 SqlSession 根据命名空间加方法名获取到可执行 sql 的结果

```java
public static void main(String[] args) {
    String resource = "config.xml";
    InputStream inputStream = null;
    SqlSessionFactory sqlSessionFactory = null;
    SqlSession session = null;
    try {
        inputStream = Resources.getResourceAsStream(resource);
        sqlSessionFactory = new SqlSessionFactoryBuilder()
                .build(inputStream);
        session = sqlSessionFactory.openSession();
        User user = session.selectOne("com.lcy.demo.UserMapper.selectUser",1);
        System.out.println(user);
    } catch (IOException e) {
        e.printStackTrace();
    }finally {
        try {
            if(inputStream != null) {
                inputStream.close();
            }
            if(session != null){
                session.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## 通过注解的形式创建 Mapper

- PermissionMapper.java
```java
public interface PermissionMapper {
    @Select("select * from permission where id = #{id}")
    Permission selectPermission(int id);
}
```
- 在 config.xml 中配置 Mapper
```xml
<mapper class="com.lcy.demo.PermissionMapper"/>
```

- 使用 PermissionMapper
```java
PermissionMapper mapper = session.getMapper(PermissionMapper.class);
Permission permission = mapper.selectPermission(1);
System.out.println(permission);
```

### 注解的优势：

> - 首先它不是基于字符串常量的，就会更安全；
- 其次，如果你的 IDE 有代码补全功能，那么你可以在有了已映射 SQL 语句的基础之上利用这个功能。

### 注解的不足：
> 对于简单语句来说，注解使代码显得更加简洁，然而 Java 注解对于稍微复杂的语句就会力不从心并且会显得更加混乱。因此，如果你需要做很复杂的事情，那么最好使用 XML 来映射语句。

## mapper 文件中的 resultType and resultMap 属性

**使用 resultType 或 resultMap，但不能同时使用。**

### resultType

从这条语句中返回的期望类型的类的完全限定名或别名。注意如果是`集合情形`，那应该是`集合可以包含的类型`，而不能是集合本身。

### resultMap

外部 resultMap 的命名引用。结果集的映射是 MyBatis 最强大的特性，对其有一个很好的理解的话，许多复杂映射的情形都能迎刃而解。

#### 简单用法

- BaseBean
```java
public class BaseBean {
    private Integer id;
    private String name;
}
```

- Permission
```java
public class Permission {
    private Integer id;
    private String name;
    private String description;
}
```

- 需求

  将从数据库读出来的 Permission 映射成 BaseBean，但是 BaseBean 的 name 要对应 Permission 的 description。

- BeanMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="BeanMapper">

    <resultMap id="PermissionBean" type="BaseBean">
        <id property="id" column="id"/>
        <result property="name" column="description"/>
    </resultMap>

    <select id="selectPermission" resultMap="PermissionBean">
        select id,description from permission where id = #{id};
    </select>
</mapper>
```

- 使用
```java
BaseBean permissionBean = session.selectOne("BeanMapper.selectPermission",1);
System.out.println(permissionBean);
```

## 数据库下划线命名转驼峰命名
```xml
<settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
</settings>
```

##  参数传递

### 通过注解
```java
Integer countByAge(@Param("isBoy") boolean isBoy, @Param("list") List<Integer> list);
```
```xml
<select id="countByAge" resultType="int">
    select count(*) from user where is_boy=${isBoy} and age in
    <foreach collection="list" item="age" open="(" close=")" separator="," index="index">
        #{age}
    </foreach>
</select>
```
通过注解传递参数的名称。

### 通过参数顺序
```java
Integer countByAge(boolean isBoy, List<Integer> list);
```
```xml
<select id="countByAge" resultType="int">
    select count(*) from user where is_boy=${arg0} and age in
    <foreach collection="arg1" item="age" open="(" close=")" separator="," index="index">
        #{age}
    </foreach>
</select>
```
- 参数 isBoy 按顺序对应 arg0 可以使用 ${0},${arg0},#{0},#{arg0} 的形式；
- 参数 list 对应 arg1 ，由于要在 foreach 中使用，只能使用 arg1 这种形式。

### 解析 map 集合

map 为 Map 类型，index 代表 key ,item 代表 value 。

```xml
<select id="selectByColumns" resultMap="BaseResultMap">
  select
  <include refid="Base_Column_List" />
  from user
  <if test="map != null">
      <where>
          <foreach collection="map" index="column" item="value">
              <if test="column != null">
                   AND ${column} = #{value}
              </if>
          </foreach>
      </where>
  </if>
</select>
```

**注意：where 节点下 AND 只能在语句开头，在语句结尾会出错。**

## 参考链接
- [MyBatis 官方文档](http://www.mybatis.org/mybatis-3/zh/index.html)
- [Mybatis的ResultMap的使用](http://www.cnblogs.com/rollenholt/p/3365866.html)
- [Settings](http://www.mybatis.org/mybatis-3/zh/configuration.html#settings)
- [Mybatis传多个参数（三种解决方案）](http://www.2cto.com/database/201409/338155.html)
