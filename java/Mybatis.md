# Mybatis

## 配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
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
        <mapper resource="HTj.xml"/>
    </mappers>
</configuration>
```

## SQL 文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="HTj">
    <select id="selectHTj" resultType="HTj">
        select * from h_tj
    </select>
    <insert id="insertHTj" parameterType="HTj">
        <selectKey keyProperty="id" resultType="int" order="AFTER" >
            SELECT LAST_INSERT_ID() as id
        </selectKey>
        insert into h_tj(ip, type) values(#{ip}, #{type})
    </insert>
</mapper>
```

## 使用

```java
String resource = "config.xml";
InputStream inputStream = null;
SqlSessionFactory sqlSessionFactory = null;
SqlSession session = null;
try {
    inputStream = Resources.getResourceAsStream(resource);
    sqlSessionFactory = new SqlSessionFactoryBuilder()
            .build(inputStream);
    session = sqlSessionFactory.openSession();


    HTj hTj = new HTj(null,"127.0.0.1",1);
    int rows = session.insert("HTj.insertHTj",hTj);
    session.commit();
    System.out.println("insert "+rows+" row");
    System.out.println(hTj.getId());

    List<HTj> list = session.selectList("HTj.selectHTj");
    System.out.println(list);

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
```

## 参考链接
