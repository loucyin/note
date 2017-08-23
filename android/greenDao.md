# greendao

## 配置环境
- 模块的 builde.gradle 下添加：
```gradle
apply plugin: 'org.greenrobot.greendao'
dependencies {
    ...
    compile 'org.greenrobot:greendao:3.0.1'
}
greendao {
    targetGenDir 'src/main/java'
}
```
greendao 配置 DaoMaster DaoSession Dao 文件的生成目录为：当前 package 下面的 model 文件夹。
编译之后自动生成。
- 工程下的 builde.gradle 下添加：
```gradle
dependencies {
    ...
    classpath 'org.greenrobot:greendao-gradle-plugin:3.0.0'
}
allprojects {
    repositories {
        ...
        mavenCentral()
    }
}
```

## 关于 id 的 autoincrement
```java
@Entity
public class AdImageInfo{
    @Id(autoincrement = true)
    private Long id;
    private String path;
    private String detailUrl;
    private String image;
}
```
设置 autoincrement = true 时，id 必须 Long 类型，而不能使用 long 基本数据类型。

## 增删改
- 封装一个 DbManager
```java
public class DbManager {
    public static final String DB_NAME = "test.db";
    private static DbManager mInstance;
    private Context mContext;
    private DaoMaster.DevOpenHelper mHelper;
    private DaoMaster mReadMaster;
    private DaoMaster mWriteMaster;
    private DbManager(Context context) {
        mContext = context;
        mHelper = new DaoMaster.DevOpenHelper(mContext,DB_NAME,null);
        mReadMaster = new DaoMaster(mHelper.getReadableDatabase());
        mWriteMaster = new DaoMaster(mHelper.getWritableDatabase());
    }

    public DaoSession getReadSession() {
        return mReadMaster.newSession();
    }

    public DaoSession getWriteSession() {
        return mWriteMaster.newSession();
    }

    public static DbManager getInstance(){
        if (mInstance == null) {
            synchronized (DbManager.class) {
                if (mInstance == null) {
                    mInstance = new DbManager(MyApplication.getInstance());
                }
            }
        }
        return mInstance;
    }
}
```
- 获取 writeSession
```java
public DaoSession getSession(){
    return DbManager.getInstance().getWriteSession();
}
```
- 实现增删改
```java
public void save(){
    getSession().insert(this);
}
public void update(){
    getSession().update(this);
}
public void delete(){
    getSession().delete(this);
}
```
greenDao 通过 DaoSession 的 insert() update() delete() 方法实现增删改。

## 查询
- 获取 readDao
```java
public static AdImageInfoDao getReadDao(){
    DaoSession session = DbManager.getInstance().getReadSession();
    return session.getAdImageInfoDao();
}
```
- 获取 QueryBuilder
```java
QueryBuilder<AdImageInfo> builder = getReadDao().queryBuilder();
builder.orderDesc(AdImageInfoDao.Properties.Id).where(AdImageInfoDao.Properties.Type.eq(0)).limit(1);
```
- 通过 QueryBuilder 进行查询
```java
// 获取列表
builder.list();
// 获取单个对象
builder.unique();
```
## 参考链接
- [GreenDao 2.2 数据库加密](http://blog.csdn.net/axuanqq/article/details/51325299)
