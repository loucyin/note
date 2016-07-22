## Idea 下创建 Gradle project
- File -> New -> project；
- 选择 Gradle ，配置 Project SDK，勾选 Java ，Next；
- GroupId 可以为空，ArtifactId 项目名称不能为空，Next；
- 如果勾选 use local gradle distribution 则配置本地 Gradle 路径，Next；
- Project Name ，Project Location 会根据 ArtifactId 自动生成，Finish。
## 添加依赖
在 build.gradle 中加入 RxJava 的依赖。
```
dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.11'
    compile 'io.reactivex:rxjava:1.1.3'
}
```
Gradle -> Refresh all Gradle projects  
项目文件夹 -> 右键 -> Open Module Settings -> Dependencies ，点击 + -> library，选中Gradle:io.reactivex:rxjava:1.1.3，Add Selected。

## 添加目录
- 项目文件夹 -> 右键 -> New -> Directory ，取名 src；
- 项目文件夹 -> 右键 -> Open Module Settings -> Sources ，选中 src 文件夹，mark as Sources;
