# gradle tips

## gradle 生成可执行 jar 包

在 build.gradle 中加入如下代码：

```groovy
jar {

    archiveName = "init.jar"

    from {
        configurations.runtime.collect {
            it.isDirectory() ? it : zipTree(it)
        }
        configurations.compile.collect {
            it.isDirectory() ? it : zipTree(it)
        }
    }

    manifest {
        attributes 'Main-Class': 'com.lcy.demo.InitProjectUtil'
    }
    exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
}
```

## gradle 不支持 tool.jar

添加依赖
```groovy
def jdkHome = System.getenv("JAVA_HOME")
dependencies {
    compile files("$jdkHome/lib/tools.jar")
}
```

## gradle 打包源码到 jar 包

```groovy
jar {
    from sourceSets.main.allSource
}
```

## gradle 复制依赖的 jar 包

```groovy
task copyJars(type:Copy){
    from configurations.runtime
    into "build/output/libs"
}
```

## 设置字符集

```groovy
tasks.withType(JavaCompile) {
    options.encoding = "UTF-8"
}
```

## gradle 上传 jar 包到 nexus

```groovy
apply plugin: 'maven'
uploadArchives {
    repositories {
        mavenDeployer {
            pom.project {
                name project.name
                packaging 'jar'
                description '测试 gradle upload'

                licenses {
                    license {
                        name "The Apache Software License, Version 2.0"
                        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                        distribution '放在内网不开源了'
                    }
                }

                developers {
                    developer {
                        id 'lcy'
                        name 'loucunyin'
                    }
                }
            }
            repository(url: "http://url/nexus/content/repositories/snapshots/") {
                authentication(userName: "username", password: "passoword")
            }
        }
    }
}
```

**pom.project 中的信息会写入到 pom.xml 中。**

## gradle.properties

gradle.properties 用于设置 gradle 运行的参数。

```
systemProp.http.proxyHost=127.0.0.1
systemProp.http.proxyPort=42885
org.gradle.jvmargs=-Xmx1536m
```

设置 gradle http 代理地址为 127.0.0.1:42885
最大堆内存： 1536M

## 参考链接

- [gradle 发布jar包到nexus](http://blog.csdn.net/bolg_hero/article/details/50418669)
