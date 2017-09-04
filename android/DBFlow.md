# DBFlow 使用

## 依赖

```groovy
repositories {

    ...

    maven { url "https://www.jitpack.io" }
}
ext{

  ...

  dbFlowVersion = '4.1.0'
  sqlCipherVersion = '3.5.7'
}
dependencies {

  ...

  // DBFlow
  kapt "com.github.Raizlabs.DBFlow:dbflow-processor:$dbFlowVersion"
  compile "com.github.Raizlabs.DBFlow:dbflow-core:$dbFlowVersion"
  compile "com.github.Raizlabs.DBFlow:dbflow:$dbFlowVersion"
  compile "com.github.Raizlabs.DBFlow:dbflow-sqlcipher:$dbFlowVersion"
  compile "net.zetetic:android-database-sqlcipher:$sqlCipherVersion@aar"
  compile "com.github.Raizlabs.DBFlow:dbflow-kotlinextensions:$dbFlowVersion"
  compile "com.github.Raizlabs.DBFlow:dbflow-rx2:$dbFlowVersion"
  compile "com.github.Raizlabs.DBFlow:dbflow-rx2-kotlinextensions:$dbFlowVersion"
}
```

## 初始化

Application onCreate 中加入：

### 简单版本

```kotlin
FlowManager.init(this)
```

### 自定义，支持 SQLCipher

初始化方法

```kotlin
private fun initDbFlow() {
    val dbConfig = DatabaseConfig.builder(AppDatabase::class.java)
            .openHelper(
                    DatabaseConfig.OpenHelperCreator(
                            { definition, listener ->
                                MyOpenHelper(definition, listener)
                            }))
            .build()
    val config = FlowConfig.builder(this)
            .addDatabaseConfig(dbConfig)
            .build()
    FlowManager.init(config)
}
```

OpenHelper

```kotlin
class MyOpenHelper(databaseDefinition: DatabaseDefinition?, listener: DatabaseHelperListener?) : SQLCipherOpenHelper(databaseDefinition, listener) {
    override fun getCipherSecret(): String {
        return "hello-db"
    }
}
```

数据库定义

```kotlin
@Database(name = AppDatabase.NAME, version = AppDatabase.VERSION)
object AppDatabase {
    const val NAME = "app_db"
    const val VERSION = 1
}
```

## 定义表格

此处有坑：

**定义的数据类必须要有默认的构造方法，也就是说定义 Kotlin data class 时，必须给属性赋初值，否则编译不通过。**

```kotlin
@Table(database = AppDatabase::class)
data class User(
        @PrimaryKey(autoincrement = true)
        var id: Int=0,
        @Column
        var name: String?=null)
```




## 参考链接

- [SQLCipher along with DBFlow](https://stackoverflow.com/questions/37939513/sqlcipher-along-with-dbflow)
