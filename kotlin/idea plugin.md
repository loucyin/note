# idea plugin

## 新建项目

- 新建 gradle 项目
- 勾选 `IntelliJ Platform Plugin` `Kotlin(Java)`
- 输入 groupId ArtifactId 一路 next ，生成项目
- import gradle project

gradle 会根据 `build.gradle` 文件中的：

```
intellij {
    version '2018.1.1'
}
```

下载指定版本的 intelliJ zip 包。zip 包有 400 多 MB ，而且下载速度很慢。将 `version` 修改为 `localPath` ，指定本地 idea 路径。

```
intellij {
    localPath='/home/gosun/tools/idea-IC-181.4445.78/'
}
```

// todo


## 参考链接

- [Plugins - Getting Started with Gradle](http://www.jetbrains.org/intellij/sdk/docs/tutorials/build_system/prerequisites.html)
- [gradle-intellij-plugin/README.md](https://github.com/JetBrains/gradle-intellij-plugin/blob/master/README.md#gradle)
