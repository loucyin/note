# mybatis generator

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

## 参考链接
- [官方文档](http://www.mybatis.org/generator/)
