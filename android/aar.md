# android 使用 aar 包

## gradle.build

配置仓库：

```groovy
repositories {
    jcenter()
    flatDir {
        dirs 'libs'
    }
}
```

引入 aar

```groovy
compile(name:'ivms_8700_sdk_library',ext:'aar')
```
