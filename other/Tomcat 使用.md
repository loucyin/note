# Tomcat 使用

## URL 传入中文参数乱码参数

设置 Tomcat 的字符集： 找到 Tomcat 下 conf -> server.xml 文件，修改代码：

```xml
<Connector port="8090"
  protocol="HTTP/1.1"   
  maxThreads="150"
  connectionTimeout="20000"   
  redirectPort="8443"
  URIEncoding="utf-8" />
```

## Tomcat 日志

通过 System.out.printf() 打印出的信息，会记录在 logs 文件夹下，类似 tomcat8-stdout.2016-06-25.log 命名的文件中。

## linux 下 Tomcat 开机自启动

修改 /etc/rc.d/rc.local ,添加下面两行脚本:

```
export JAVA_HOME=/usr/local/jdk1.7.0_03
/usr/local/apache-tomcat-7.0.32/bin/startup.sh start
```
