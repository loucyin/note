## 下载 tomcat
tomcat 的安装要在 Java 开发环境配置好以后进行。
[下载链接](http://mirror.bit.edu.cn/apache/tomcat/tomcat-8/v8.0.33/bin/apache-tomcat-8.0.33.tar.gz)
## 解包
两种解包方式，第一种所有者为用户，第二种所有者为 root 。
- 归档管理器  
提取，然后移动到 /opt/tomcat
- tar 命令直接解压到 /opt/tomcat  
```
sudo tar xvf apache-tomcat-8*tar.gz -C /opt/tomcat
cd /opt/tomcat
sudo chown username -R ./
sudo chgrp username -R ./
```

## 启动、关闭tomcat  
```
cd /opt/tomcat/bin
./startup.sh
./shutdown.sh
```

## 配置用户名和密码
```
gedit /opt/tomcat/conf/tomcat-users.xml
```
插入如下代码：
```xml
<role rolename="manager-gui"/>
<user username="tomcat" password="s3cret" roles="manager-gui"/>
```
启动 tomcat ，浏览器中输入： localhost:8080 ,就可以输入刚才的用户名和密码进入管理界面。
