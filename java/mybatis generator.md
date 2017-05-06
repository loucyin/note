# mybatis generator

## 依赖
```groovy
compile 'mysql:mysql-connector-java:5.1.40'
compile 'org.mybatis:mybatis:3.4.2'
compile group: 'org.mybatis.generator', name: 'mybatis-generator-core', version: '1.3.5'
```

## config 文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">

<generatorConfiguration>
    <context id="TestTables" targetRuntime="MyBatis3">
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/mydb?useSSL=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC"
                        userId="root"
                        password="gc2017">
        </jdbcConnection>

        <javaTypeResolver >
            <property name="forceBigDecimals" value="false" />
        </javaTypeResolver>

        <javaModelGenerator targetPackage="com.gosun.daily.report.model" targetProject="E:\workspace\DailyReport\src\main\java">
            <property name="enableSubPackages" value="true" />
            <property name="trimStrings" value="true" />
        </javaModelGenerator>

        <sqlMapGenerator targetPackage="mapper"  targetProject="E:\workspace\DailyReport\src\main\resources">
            <property name="enableSubPackages" value="true" />
        </sqlMapGenerator>

        <javaClientGenerator type="XMLMAPPER" targetPackage="com.gosun.daily.report.mapper"  targetProject="E:\workspace\DailyReport\src\main\java">
            <property name="enableSubPackages" value="true" />
        </javaClientGenerator>

        <table tableName="user" enableCountByExample="false" enableDeleteByExample="false" enableSelectByExample="false" enableUpdateByExample="false"/>
    </context>
</generatorConfiguration>
```

## java 代码
```java
public static void main(String[] args) {
    List<String> warning = new ArrayList<>();
    ConfigurationParser parser = new ConfigurationParser(warning);
    InputStream inputStream = MysqlGenerator.class.getClassLoader().getResourceAsStream("mybatis/generator_config.xml");
    Configuration configuration = null;
    try {
        configuration = parser.parseConfiguration(inputStream);
    } catch (IOException | XMLParserException e) {
        e.printStackTrace();
    }
    ShellCallback callback = new DefaultShellCallback(true);
    MyBatisGenerator generator = null;
    try {
        generator = new MyBatisGenerator(configuration,callback,warning);
    } catch (InvalidConfigurationException e) {
        e.printStackTrace();
    }
    ProgressCallback progressCallback = new NullProgressCallback();
    try {
        generator.generate(progressCallback);
    } catch (SQLException | IOException | InterruptedException e) {
        e.printStackTrace();
    }

    System.out.println(warning);
}
```

## 自定义代码生成器
Mybatis generator 可以通过配置context 节点下的 plugin ，自定义代码生成。
```xml
<plugin type="com.gosun.isap.generator.MyPlugin"/>
```
MyPlugin
在 UserMapper.java 文件中添加一个接口：
```java
List<User> selectAll();
```
在 UserMapper.xml 文件中添加：
```
<select id="selectAll" resultType="com.demo.User">
  select * from User
</select>
```

```java
public class MyPlugin extends PluginAdapter{

    @Override
    public boolean clientGenerated(Interface interfaze, TopLevelClass topLevelClass, IntrospectedTable introspectedTable) {
        // 添加方法
        Method method = new Method("selectAll");
        FullyQualifiedJavaType returnType = new FullyQualifiedJavaType("java.util.List<"+introspectedTable.getBaseRecordType()+">");
        method.setReturnType(returnType);
        interfaze.addMethod(method);

        // 添加 import
        interfaze.addImportedTypes(new FullyQualifiedJavaType("java.util.List"));

        return super.clientGenerated(interfaze, topLevelClass, introspectedTable);
    }

    @Override
    public boolean sqlMapDocumentGenerated(Document document, IntrospectedTable introspectedTable) {
        XmlElement parentElement = document.getRootElement();

        // 添加 xml 节点
        XmlElement xmlElement = new XmlElement("select");
        xmlElement.addAttribute(new Attribute("id","selectAll"));
        xmlElement.addAttribute(new Attribute("resultType",introspectedTable.getBaseRecordType()));
        xmlElement.addAttribute(new TextElement("select * from "+introspectedTable.getFullyQualifiedTableNameAtRuntime()));
        parentElement.addElement(xmlElement);

        return super.sqlMapDocumentGenerated(document, introspectedTable);
    }

    @Override
    public boolean validate(List<String> warnings) {
        return true;
    }
}
```

## 返回自增主键
```xml
<table tableName="rc_template">
    <generatedKey column="ID" sqlStatement="MySql" identity="true"/>
</table>
```

## 参考链接
- [官方文档](http://www.mybatis.org/generator/)
- [mybatis自定义代码生成器（Generator）](http://blog.csdn.net/yangchao13341408947/article/details/52510766)
- [Mybatis generator 添加记录时返回自增主键](http://blog.csdn.net/u011403655/article/details/50696341)
